# SmolVLM-256M-Instruct Model Conversion
This repository contains instructions for converting the SmolVLM-256M-Instruct model into formats suitable for Axera devices. The model is split into a Vision Encoder and a Language Model; they are converted separately.

## Part 1 — Convert the Vision Encoder

Export the Vision Encoder to ONNX, then use `pulsar2 build` to convert the ONNX file to an Axera `.axmodel`.

### 1. Create a virtual environment

```
conda create -n smolvlm python=3.12 -y
conda activate smolvlm
```

### 2. Install dependencies

```
pip install -r requirements.txt
```
This code depends on `transformers==4.49.0`; we recommend using that exact version as other releases may be incompatible.

### 3. Export model (PyTorch -> ONNX)

Before exporting to ONNX, download the model from Hugging Face or ModelScope. In these instructions the model directory is assumed to be `../SmolVLM-256M-Instruct/`.

You can run `bash export.sh` to export the model directly. Detailed steps are shown below.

1) Run the model and verify the float model output
```
python run.py ../SmolVLM-256M-Instruct/
```

2) Export the ONNX model
```
python export.py ../SmolVLM-256M-Instruct/
```
This step generates `Qwen2.5-VL-3B-Instruct_vision.onnx` and `Qwen2.5-VL-3B-Instruct_vision.onnx.data`, separating the graph and parameter data.

3) Test the ONNX model

```
python test_onnx.py ../SmolVLM-256M-Instruct/
```
This step uses the ONNX model in place of the vision encoder for inference; compare the output with `run.py` to confirm they match.

### 4. Convert ONNX -> Axera (.axmodel)

Use the `Pulsar2` toolchain to convert the ONNX model into a `.axmodel` file suitable for Axera NPUs. This usually involves these steps:

-- Generate a PTQ calibration dataset for this model
-- Use `pulsar2 build` to perform quantization (PTQ) and compile the model. For more details, see the Pulsar2 docs: https://pulsar2-docs.readthedocs.io/

1) PTQ calibration dataset
Download the existing dataset [imagenet-calib.tar](https://github.com/techshoww/SmolVLM-256M-Instruct.axera/releases/download/v1.0.0/imagenet-calib.tar) or create your own by packing images into a tar file:
```
tar  -cvf calib.tar {your images}
```

2) Model conversion

* Update the configuration
 
Check the `config.json` file and set the `calibration_dataset` path to the dataset location created in step 1.

* Run Pulsar2 build

参考命令如下：

```
pulsar2 build --input SmolVLM-256M-Instruct_vision.onnx --config config.json --output_dir build-output --output_name SmolVLM-256M-Instruct_vision.axmodel --target_hardware AX650 --compiler.check 0
```
After compilation move `build-output/SmolVLM-256M-Instruct_vision.axmodel` to `../SmolVLM-256M-Instruct-AX650/`.

## Part 2 — Convert Language Model

### 1) Convert Language Model
执行命令
```
pulsar2 llm_build --input_path ../SmolVLM-256M-Instruct/ --output_path ../SmolVLM-256M-Instruct-AX650/ --kv_cache_len 1023 --hidden_state_type bf16 --prefill_len 320 --parallel 32 --chip AX650
```
The `prefill_len` value sets the maximum number of tokens during the prefill stage. Set to an appropriate value for your use case.
`kv_cache_len` is the total length of tokens for prefill and decode stages combined.

### 2) Extract token embeddings from the Language Model

执行命令  
```
bash tools/embed_process.sh ../SmolVLM-256M-Instruct/ ../SmolVLM-256M-Instruct-AX650/
```
### 3) Copy the configuration files
```
cp ../SmolVLM-256M-Instruct/*.json ../SmolVLM-256M-Instruct-AX650/
```

The conversion is complete. Upload `../SmolVLM-256M-Instruct-AX650/` to your Axera device for deployment.