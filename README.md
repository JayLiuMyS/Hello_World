# include "cv.h"
# include "cxcore.h"
# include "highgui.h"
void main()
{
	//定义的变量 初始化变量
	IplImage* pImage = NULL;
	IplImage* pImgSobelgray = NULL;
	IplImage* pImg8u = NULL;
	IplImage* pImg8uSmooth = NULL;
	IplImage* pImgColor = NULL;
	IplImage* pImgSobelcolor = NULL;
	IplImage* pImgPlanes[3]={0,0,0};
	IplImage* img=cvLoadImage("fig_1.jpg",1);
	pImage = cvCloneImage(img);

	//建立和原始图像一样的内存区
	pImg8u = cvCreateImage(cvGetSize(pImage),IPL_DEPTH_8U,1);
	pImg8uSmooth = cvCreateImage(cvGetSize(pImage),IPL_DEPTH_8U,1);
	//对灰度图像进行 Sobel 变换
	//将彩色图像转换为灰度图像
	cvCvtColor(pImage,pImg8u,CV_BGR2GRAY);
    //对图像进行高斯滤波
	cvSmooth(pImg8u,pImg8uSmooth,CV_GAUSSIAN,3,0,0);
	//建立新的图像内存区，图像元素的位深度位 IPL_DEPTH_16S 16位有符号整型
	//因为cvSobel函数要求目标图像必须是16bit图像
	pImgSobelgray = cvCreateImage(cvGetSize(pImage),IPL_DEPTH_16S,1);
	//计算一阶x方向的图像差分，可根据需要设置参数
	cvSobel(pImg8uSmooth,pImgSobelgray,0,1,3);
	//将图像格式再转换回来用于显示
	cvConvertScaleAbs(pImgSobelgray,pImg8u,1,0);
	//创建窗口，显示图像
	cvvNamedWindow("Sobel gray Image",1);
	cvvShowImage("Sobel gray Image",pImg8u);
	//对彩色图进行Sobel变换
	//建立3个图像内存区，分别存储图像的三个通道，图像元素的位深度为IPL_DEPTH_8U
	int i;
	for(i = 0; i < 3; i++)
	{
	pImgPlanes[i] = cvCreateImage(cvSize(pImage->width,pImage->height),8,1);
	}
	//建立一新图像内存区，图像元素的位深度设为IPL_DEPTH_16S有符号16位整型
	pImgSobelcolor = cvCreateImage(cvSize(pImage -> width,pImage -> height),IPL_DEPTH_16S,1);
	//要求输出图像是16位有符号的
	pImgColor = cvCreateImage(cvSize(pImage -> width,pImage -> height),8,3);
	//彩色图像分为3个单通道图像

	cvSplit(pImage,pImgPlanes[0],pImgPlanes[1],pImgPlanes[2],0);
	for(i = 0; i < 3;i ++)
	{
	//对每个通道图像进行Sobel变换
		cvSobel(pImgPlanes[i],pImgSobelcolor,0,1,3);
		//转化为8位的图像
		cvConvertScaleAbs(pImgSobelcolor,pImgPlanes[i],1,0);
	}
	//将各通道图像进行合并
	cvMerge(pImgPlanes[0],pImgPlanes[1],pImgPlanes[2],0,pImgColor);
	//创建窗口，显示图像
	cvvNamedWindow("Sobel color Image",1);
	cvvShowImage("Sobel color Image",pImgColor);
	//等待按键
	cvWaitKey(0);
	//销毁窗口
	cvvDestroyWindow("Sobel gray Image");
	cvvDestroyWindow("Sobel color Image");
	//将程序开始定义的变量释放
	cvReleaseImage(& pImage);
	cvReleaseImage(& pImgSobelgray);
	cvReleaseImage(& pImgSobelcolor);
	cvReleaseImage(& pImg8u);
	cvReleaseImage(& pImg8uSmooth);

}
