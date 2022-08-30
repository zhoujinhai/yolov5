ref: https://zhuanlan.zhihu.com/p/275989233

### 1. export pt model to onnx
这里如果参照[ncnn中yolov5](https://github.com/Tencent/ncnn/blob/master/examples/yolov5.cpp)前推的代码，
需要加上--train，即去掉结果合并操作，直接有三个输出
```bash
python export.py --weights ./runs/train/exp3/weights/best.pt --include onnx --train
```

### 2. onnx to ncnn
- onnx2ncnn
利用ncnn库的./onnx2ncnn 工具进行转换
```bash
onnx2ncnn *.onnx *.param *.bin
```

<font color="red">转换时如果报以下错误提示</font> ，则利用onnxsim库先对onnx模型进行简化再转成ncnn
```bash
python -m onnxsim tooth.onnx tooth_sim.onnx
```

```text
Shape not supported yet!
Gather not supported yet!
Unsupported unsqueeze axes !
Unknown data type 0
```

### 3. 动态输入
<font color="red">如果推理结果密密麻麻布满整个图片，则有可能是动态输入尺寸造成的</font>
如果图片是动态输入，则需要将导出的param文件进行修改，因为在最后 Reshape 层把输出grid数写死了
用Netron打开param文件，从中找到三个输出前的reshape操作，将6400 1600 400 都更改为-1即可


### 4. ncnn其他相关操作
- ncnnoptimize
对ncnn模型中部分算子进行合并优化等，ref: https://zhuanlan.zhihu.com/p/93017149
```bash
ncnnoptimize *.param *.bin new.param new.bin flag
```
其中flag为0指fp32, 为1指fp16

- ncnn2mem
将ncnn模型中的可见字符去除，执行以下命令可生成*.param.bin 和两个静态数组的代码文件
```bash
ncnn2mem *.param *.bin *.id.h *.mem.h
```

- 合并param和bin文件
```bash
cat *.param.bin *.bin > *_all.bin
```

- 加载方式
  - 原始param和bin
    ```C++
    ncnn::Net net;
    net.load_param("*.param");
    net.load_model("*.bin");
    ```
    
  - 二进制param.bin和bin
    ```C++
    ncnn::Net net;
    net.load_param_bin("*.param.bin");
    net.load_model("*.bin");
    ```
  - 加载数据模型
       ```C++
       #include "*.mem.h"
       ncnn::Net net;
       net.load_param(*_param_bin);
       net.load_model(*_bin);
       ```
   - 加载合并后bin
       ```C++
        #include "net.h"
        FILE* fp = fopen("*-all.bin", "rb");
        net.load_param_bin(fp);
        net.load_model(fp);
        fclose(fp);
       ```