-
- 简单使用
	- 安装
		- ```sh
		  git clone https://github.com/ultralytics/yolov5  # clone
		  cd yolov5
		  pip install -r requirements.txt  # install
		  ```
	- 在 [此处](https://github.com/meituan/YOLOv6/releases/tag/0.1.0) 下载训练好的数据集（那几个pt文件），放到sources目录下
	- 运行命令识别
		- ```sh
		  python ./tools/infer.py --weights ./configs/yolov6s.pt --source ./source/001.jpg
		  ```
	- 效果图在 .\runs\inference\exp下
	  collapsed:: true
		- ![img.jpg](../assets/img_1656401729129_0.jpg)
		- ![002.jpg](../assets/002_1656401714172_0.jpg)
		- ![001.jpg](../assets/001_1656401761623_0.jpg)
	-