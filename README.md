## Quick Start

1. Git clone yolov5 project of u version and install requirements.txt 

   ```
   git clone https://github.com/ultralytics/yolov5
   cd yolov5
   pip install -r requirements.txt
   pip install coremltools > =4.1 onnx > =1.9.0 scikit-learn==0.19.2
   ```

2. To export onnx with 640x640, the batch size must be 1,if --train is not added, there will be many redundant layers

   ```
   python export.py --weights yolov5s.pt --img 640 --batch 1 --train   
   ```

3. Onnx simplifer simplified model

   ```
   python -m onnxsim yolov5s.onnx yolov5s-sim.onnx
   ```

4. Transforming ncnn model

   ```
   onnx2ncnn yolov5s-sim.onnx yolov5s.param yolov5s.bin
   ```

5. Edit yolov5s.param file,Delete layer of lines 4-13

   ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210510184740477.png)

6. Add YoloV5Focus layer under Input layer

   ```
   YoloV5Focus              focus                    1 1  images 159
   ```

7. Change 173 in the second line to 164

   ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210510185124109.png#pic_center)

8. Support dynamic dimension input,Change the numbers in the red box to - 1

   ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210510192419596.png)

9. ncnnoptimize optimization

   ```
   ./ncnnoptimize yolov5s.param yolov5s.bin yolov5s-opt.param yolov5s-opt.bin 1
   ```

10. In ncnn example yolov5.cpp, three ex. extract (", out) are found; Args fills in the three parameters permute in yolov5s.param

11. Recompile and run yolov5. cpp

    ```
    ncnn/build# make install
    ncnn/build/examples# ./yolov5 1.jpg
    ```

    

## Thanks

https://github.com/Tencent/ncnn

https://github.com/ultralytics/yolov5/releases

https://blog.csdn.net/flyfish1986/article/details/116604907

