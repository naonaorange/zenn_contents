---
title: "WPF PrismとNyARToolKitでマーカー位置推定"
emoji: "😀"
type: "tech"
topics: ["20170220","wpf","prism","artoolkit"]
published: true
---
マーカーの位置推定を行うプログラムです。
以下のことを行います。
1. カメラ座標系からマーカー座標系への同時変換行列を取得(Consoleに出力)
1. マーカーにグラフィックを描画して、カメラ画像を表示

これでは、NyARToolKitCS, OpenCVSharp,Prism.MVVMを使用しています。
![](/images/20170220_wpf_prism_nyartoolkit/1.png)

初めにXamlです。
次にViewModelです。
```cs
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

using Microsoft.Practices.Prism.Mvvm;
using Microsoft.Practices.Prism.Commands;

using System.ComponentModel;
using System.Data;
using System.Drawing;

using System.Windows.Threading;
using System.Threading;
using System.Windows.Media.Imaging;

namespace NyARCaptureTest
{
    class MainWindowViewModel: BindableBase
    {
        NyARPositionEstimation NyAr;
        bool isTask;

        /// 

        /// Notifying Property Change
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

        private DelegateCommand startCommand;
        public DelegateCommand StartCommand
        {
            get{ return startCommand ?? (startCommand = new DelegateCommand(Start));}
        }
        private async void Start()
        {
            NyAr.Start();

            isTask = true;
            await ShowImage();
        }

        private DelegateCommand stopCommand;
        public DelegateCommand StopCommand
        {
            get { return stopCommand ?? (stopCommand = new DelegateCommand(Stop)); }
        }
        private void Stop()
        {
            NyAr.Stop();
            isTask = false;
        }


        public MainWindowViewModel()
        {
            NyAr = new NyARPositionEstimation();
        }

        /// 

        /// Task
        /// 

        /// 
        private async Task ShowImage()
        {

            while (isTask)
            {
                if (NyAr.IsCaptureOK)
                {
                    Bmp = NyAr.GetImage();
                    Console.WriteLine(NyAr.Matrix);
                }

                await Task.Delay(30);
            }
        }
    }
}
```
最後にModelです。
マーカーの位置推定はNyARToolKitCSのサンプルを参考にNyARPositionEstimationクラスを作りました。
基本的に処理はこちらで行っています。
```cs
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

using System.ComponentModel;
using System.Data;
using System.Drawing;

using System.Drawing.Imaging;
using jp.nyatla.nyartoolkit.cs;
using jp.nyatla.nyartoolkit.cs.core;
using jp.nyatla.nyartoolkit.cs.detector;
using NyARToolkitCSUtils.Capture;
using NyARToolkitCSUtils.Direct3d;
using System.IO;

using OpenCvSharp;
using OpenCvSharp.CPlusPlus;
using OpenCvSharp.Extensions;

using System.Windows.Threading;
using System.Windows.Media.Imaging;

using System.Numerics;  //.Net Framework 4.6以上

namespace NyARCaptureTest
{
    class NyARPositionEstimation : CaptureListener
    {
        private NyARToolkitCSUtils.Capture.CaptureDevice m_cap;
        private const String AR_CODE_FILE = "patt.hiro";
        private const String AR_CAMERA_FILE = "camera_para.dat";
        private NyARSingleDetectMarker m_ar;
        private DsRgbRaster m_raster;

        private Mat Img;
        public Matrix4x4 Matrix { get; private set; }

        public bool IsCaptureOK { get; private set; }


        public NyARPositionEstimation()
        {
            IsCaptureOK = false;
            //ARの設定
            //AR用カメラパラメタファイルをロード
            NyARParam ap = NyARParam.loadFromARParamFile(File.OpenRead(AR_CAMERA_FILE), 320, 240);

            //AR用のパターンコードを読み出し 
            NyARCode code = NyARCode.loadFromARPattFile(File.OpenRead(AR_CODE_FILE), 16, 16);

            //NyARDoubleMatrix44 result_mat = new NyARDoubleMatrix44();
            //計算モードの設定
            //キャプチャを作る
            /**************************************************
            このコードは、0番目（一番初めに見つかったキャプチャデバイス）
            を使用するようにされています。
            複数のキャプチャデバイスを持つシステムの場合、うまく動作しないかもしれません。
            n番目のデバイスを使いたいときには、CaptureDevice cap=cl[0];←ここの0を変えてください。
            手動で選択させる方法は、SimpleLiteDirect3Dを参考にしてください。
            **************************************************/
            CaptureDeviceList cl = new CaptureDeviceList();
            NyARToolkitCSUtils.Capture.CaptureDevice cap = cl[0];
            cap.SetCaptureListener(this);
            cap.PrepareCapture(320, 240, 30);
            this.m_cap = cap;
            //ラスタを作る。
            this.m_raster = new DsRgbRaster(cap.video_width, cap.video_height);
            //１パターンのみを追跡するクラスを作成
            this.m_ar = NyARSingleDetectMarker.createInstance(ap, code, 80.0);
            this.m_ar.setContinueMode(false);
        }


        public void Start()
        {
            this.m_cap.StartCapture();
        }

        public void Stop()
        {
            this.m_cap.StopCapture();
        }


        public WriteableBitmap GetImage()
        {
            return Img.ToWriteableBitmap();
        }


        public void OnBuffer(NyARToolkitCSUtils.Capture.CaptureDevice i_sender, double i_sample_time, IntPtr i_buffer, int i_buffer_len)
        {
            int w = i_sender.video_width;
            int h = i_sender.video_height;
            int s = w * (i_sender.video_bit_count / 8);

            Bitmap b = new Bitmap(w, h, s, PixelFormat.Format32bppRgb, i_buffer);

            // If the image is upsidedown
            b.RotateFlip(RotateFlipType.RotateNoneFlipY);
            Img = b.ToMat();

            //ARの計算
            this.m_raster.setBuffer(i_buffer, i_buffer_len, i_sender.video_vertical_flip);
            if (this.m_ar.detectMarkerLite(this.m_raster, 100))
            {
                NyARDoubleMatrix44 result_mat = new NyARDoubleMatrix44();
                this.m_ar.getTransmationMatrix(result_mat);
                Matrix = ConvertNyARDoubleMatrix44ToMatrix4x4(result_mat);

                //Console.WriteLine("Maker is found.");

                NyARSquare square = m_ar.refSquare();

                var point = new OpenCvSharp.CPlusPlus.Point[4];
                for (int i = 0; i < 4; i++)
                {
                    point[i] = new OpenCvSharp.CPlusPlus.Point(square.sqvertex[i].x, square.sqvertex[i].y);
                }
                Img.FillConvexPoly(point, new Scalar(0, 0, 255));
            }
            else
            {
                //Console.WriteLine("Maker is NOT found.");
            }

            IsCaptureOK = true;
        }

        private Matrix4x4 ConvertNyARDoubleMatrix44ToMatrix4x4(NyARDoubleMatrix44 m)
        {
            return new Matrix4x4(   (float)m.m00,
                                    (float)m.m01,
                                    (float)m.m02,
                                    (float)m.m03,
                                    (float)m.m10,
                                    (float)m.m11,
                                    (float)m.m12,
                                    (float)m.m13,
                                    (float)m.m20,
                                    (float)m.m21,
                                    (float)m.m22,
                                    (float)m.m23,
                                    (float)m.m30,
                                    (float)m.m31,
                                    (float)m.m32,
                                    (float)m.m33);
        }
    }
}
```