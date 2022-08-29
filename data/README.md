ref: https://github.com/ultralytics/yolov5/wiki/Train-Custom-Data
### 1. data format
>
>  - yolov5
>    - images
>       - train
>          - *.jpg
>       - val
>          - *.jpg
>
>    - labels
>       - train
>          - *.txt
>       - val
>          - *.txt
> 


### 2. train
```bash
python train.py --img 640 --batch 16 --epochs 300 --data ./data/tooth.yaml --weights ./weights/yolov5s.pt --cfg yolov5s.yaml
```

### 3. test
自动测试data目录下images下的所有图片
```bash
python detect.py --weights ./runs/train/exp3/weights/best.pt
```

### 4. export to onnx
导出前先进行替换，否则会报silu not support yet! (python: 3.6.9, torch: torch-1.7.1+cu110)
 model = replace_module(model, nn.SiLU, SiLU)

```bash
python export.py --weights ./runs/train/exp3/weights/best.pt --include onnx
```