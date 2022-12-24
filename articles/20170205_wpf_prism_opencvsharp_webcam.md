---
title: "WPF PrismとOpenCVSharpでWebカメラの画像表示"
emoji: "😀"
type: "tech"
topics: ["20170205","wpf","prism","opencv"]
published: true
---
OpenCVSharpを使ってWebカメラの画像を取得し、WPFのImageで表示します。
プロジェクトは以下のようになっていて、View、Model、ViewModelそれぞれ1ファイルづつあります。

初めに、ViewのMainWindow.xamlです。
次にViewModelのMainWindowViewModel.cs。
画像の表示は別タスクにしていて、async/awaitで非同期処理を行っています。
```cs
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using CaptureCamera.Model;

using Microsoft.Practices.Prism.Mvvm;
using Microsoft.Practices.Prism.Commands;
using System.Windows.Media.Imaging;

namespace CaptureCamera.ViewModel
{
    class MainWindowViewModel: BindableBase
    {
        Camera camera;
        bool isTask = true;

        public MainWindowViewModel()
        {
        }

        /// 

        /// Change Property
        /// 

        private WriteableBitmap bmp;
        public WriteableBitmap Bmp
        {
            get { return bmp; }
            set { SetProperty(ref bmp, value); }
        }


        /// 

        /// DelegateCommand
        /// 

        private DelegateCommand startCaptureCommand;
        public DelegateCommand StartCaptureCommand
        {
            get { return startCaptureCommand ?? (startCaptureCommand = new DelegateCommand(StartCapture)); }
        }
        private async void StartCapture()
        {
            camera = new Camera();
            await ShowImage();
        }

        private DelegateCommand stopCaptureCommand;
        public DelegateCommand StopCaptureCommand
        {
            get { return stopCaptureCommand ?? (stopCaptureCommand = new DelegateCommand(StopCapture)); }
        }
        private void StopCapture()
        {
            isTask = false;
        }


        /// 

        /// Task
        /// 

        /// 
        private async Task ShowImage()
        {
            while (isTask)
            {
                Bmp = camera.Capture();
                if (Bmp == null) break;

                await Task.Delay(30);
            }
        }
    }
}
```

最後にModelのCamera.cs。
```cs
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

using Microsoft.Win32;
using System.Windows.Media.Imaging;
using OpenCvSharp;
using OpenCvSharp.CPlusPlus;
using OpenCvSharp.Extensions;

namespace CaptureCamera.Model
{
    class Camera
    {
        VideoCapture capture;
        Mat frame;

        public Camera()
        {
            capture = new VideoCapture(0);
            if (!capture.IsOpened())
                throw new Exception("capture initialization failed");
            frame = new Mat();
        }

        public WriteableBitmap Capture()
        {
            capture.Read(frame);
            if (frame.Empty())
                return null;

            return frame.ToWriteableBitmap();
        }
    }
}
```