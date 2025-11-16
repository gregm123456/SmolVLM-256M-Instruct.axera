# SmolVLM-256M-Instruct.axera
SmolVLM-256M-Instruct DEMO on Axera

- Prebuilt models available at [models](https://huggingface.co/AXERA-TECH/SmolVLM-256M-Instruct); if you need to convert them yourself, see [Model Conversion](/model_convert/README.md)
- [C++ demo](./cpp)

Recursively clone this repository
```
git clone --recursive https://github.com/techshoww/ax-llm.git
```

## Supported platforms

- [x] AX650N
- [x] AX630C

## Model conversion

[模型转换](./model_convert/README.md)

## Board deployment

- AX650N devices come pre-installed with Ubuntu 22.04
 - Log in to the AX650N board as root
 - Ensure the device is connected to the internet so `apt install`, `pip install` and similar commands work
 - Verified devices: AX650N DEMO Board, AiXinPai Pro (AX650N), AiXinPai 2 (AX630C)

### Python API run

#### Requirements

Copy `npu_python_llm` to an AX650N development board or AiXinPai Pro that has a Python environment.
Run the commands below to install pyaxengine
```
cd {your path to npu_python_llm}/axengein 
pip install -e .
``` 

#### Add environment variables

Add the following lines to `/root/.bashrc` (verify the exact path for your setup), then reconnect the terminal or run `source ~/.bashrc`

```
export LD_LIBRARY_PATH={your path to npu_python_llm}/engine_so/:$LD_LIBRARY_PATH
``` 

#### Run

Run the command on the development board

```
python3 infer_axmodel.py
```  
**Input**
Image:
![demo.jpg](assets/demo.jpg)
Text:
```
Can you describe this image?
```
**Output**
```
The image depicts a large, historic statue of Liberty, located in New York City. The statue is a prominent landmark in the city and is known for its iconic presence and historical significance. The statue is located on Liberty Island, which is a part of the Empire State Building complex. The statue is made of bronze and is mounted on a pedestal. The pedestal is rectangular and has a weathered look, suggesting it has been in use for a long time. The statue is surrounded by a large, open area, which is likely a plaza or a plaza park.

The statue is surrounded by a large, open space, which is likely a plaza or a plaza park. The space is filled with trees and other greenery, and there is a clear view of the city skyline, which includes the Empire State Building and other notable landmarks. The sky is clear and blue, indicating that it is a sunny day.

In the background, there are tall buildings that are part of the Empire State Building complex. These buildings are modern and have a sleek, contemporary design. The buildings are well-maintained and appear to be part of the same building complex.

The statue is positioned in the center of the image, with the plaza in the foreground. The plaza is surrounded by trees and other greenery, and there is a clear view of the city skyline. The sky is clear and blue, indicating that it is a sunny day.

The statue is made of bronze and is mounted on a pedestal. The pedestal is rectangular and has a weathered look, suggesting it has been in use for a long time. The statue is surrounded by a large, open area, which is likely a plaza or a plaza park.

The statue is surrounded by a large, open space, which is likely a plaza or a plaza park. The space is filled with trees and other greenery, and there is a clear view of the city skyline. The sky is clear and blue, indicating that it is a sunny day.

In summary, the image depicts the Statue of Liberty in New York City, surrounded by a large open plaza with trees and other greenery. The statue is positioned in the center of the image, surrounded by a large open space with trees and other greenery. The sky is clear and blue, indicating a sunny day.<end_of_utterance>
```


## Inference speed

### python demo
| Stage | Time |
|------|------|
| Image Encoder (512x512) | 121 ms  | 
| Prefill (prefill_len 320) |  512ms    |
| Decode  |  11.6 token/s |

### C++ demo
| Stage | Time |
|------|------|
| Image Encoder (512x512) | 120 ms  | 
| Prefill (prefill_len 128)|  57ms    |
| Decode  |  77 token/s |

Python code performance is limited; we recommend using the [C++ code](./cpp) for better performance.

## Technical discussion

- Github issues
- QQ 群: 139953715
