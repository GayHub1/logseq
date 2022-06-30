-
- 简单使用
  collapsed:: true
	- 安装
	  collapsed:: true
		- ```sh
		  git clone https://github.com/ultralytics/yolov5  # clone
		  cd yolov5
		  pip install -r requirements.txt  # install
		  ```
	- 在 [此处](https://github.com/meituan/YOLOv6/releases/tag/0.1.0) 下载训练好的数据集（那几个pt文件），放到sources目录下
	- 运行命令识别
	  collapsed:: true
		- ```sh
		  python ./tools/infer.py --weights ./configs/yolov6s.pt --source ./source/001.jpg
		  ```
		- 识别rtsp流
		  collapsed:: true
			- ```sh
			  python ./detect.py --weights yolov5s.pt --source rtsp://admin:Gdsc2005@192.168.31.207
			  ```
	- 效果图在 .\runs\inference\exp下
	  collapsed:: true
		- ![img.jpg](../assets/img_1656401729129_0.jpg)
		- ![002.jpg](../assets/002_1656401714172_0.jpg)
		- ![001.jpg](../assets/001_1656401761623_0.jpg)
	-
- 训练数据集
	- 安装 [Anaconda](https://www.anaconda.com/)
	- 参考 [手把手教你用YOLOv5训练自己的数据集（从Windows环境配置到模型部署）]((https://blog.csdn.net/weixin_44936889/article/details/110661862)(https://www.icode9.com/content-3-774443.html))