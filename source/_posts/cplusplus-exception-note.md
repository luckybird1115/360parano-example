---
title: C#winform程序全局异常处理
date: 2019-04-10 10:39:18
tags: 
   - C#
categories: C# 
---
全局处理未处理异常
<!--more-->
# program.cs入口
```
using GDZLCHECK.Forms;
using NLog;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using System.Windows.Forms;
namespace GDZLCHECK
{
    static class Program
    {
        /// <summary>
        /// 应用程序的主入口点。
        /// </summary>
        [STAThread]
        static void Main()
        {
            try
            {
                Application.EnableVisualStyles();
                Application.SetCompatibleTextRenderingDefault(false);

                //处理未捕获的异常
                Application.SetUnhandledExceptionMode(UnhandledExceptionMode.CatchException);
                //处理UI线程异常
                Application.ThreadException += new System.Threading.ThreadExceptionEventHandler(Application_ThreadException);
                //处理非UI线程异常
                AppDomain.CurrentDomain.UnhandledException += new UnhandledExceptionEventHandler(CurrentDomain_UnhandledException);                           
                ESRI.ArcGIS.RuntimeManager.Bind(ESRI.ArcGIS.ProductCode.EngineOrDesktop);
                Application.EnableVisualStyles();
                Application.SetCompatibleTextRenderingDefault(false);
                Application.Run(new MainForm());
                glExitApp = true;
            }
            catch (Exception e)
            {
                MessageBox.Show("请确保安装有ArcGIS和Office,且ArcGIS License已启动");
                Application.Exit();  
                
            }

        }
        static bool glExitApp = false;
        static void CurrentDomain_UnhandledException(object sender, UnhandledExceptionEventArgs e)
        {
            MessageBox.Show("抱歉，您的操作没有能够完成，请再试一次或者联系软件提供商");   
            Logger logger = LogManager.GetCurrentClassLogger();
            logger.Error("CurrentDomain_UnhandledException");
            logger.Error("IsTerminating:" + e.IsTerminating.ToString());
            logger.Error(e.ExceptionObject.ToString());
        }

        static void Application_ThreadException(object sender, System.Threading.ThreadExceptionEventArgs e)
        {
            MessageBox.Show("抱歉，您的操作没有能够完成，请再试一次或者联系软件提供商");
            Logger logger = LogManager.GetCurrentClassLogger();
            logger.Error("Application_ThreadException:"+e.Exception.Message);           
            logger.Error(e.Exception);
            //throw new NotImplementedException();
        }
    }
}


```
