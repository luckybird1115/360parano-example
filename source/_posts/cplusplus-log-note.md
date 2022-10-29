---
title: C# 使用NLog显示和输出日志
date: 2019-04-10 11:15:48
tags: C#
categories: C#
---
使用NLog插件显示和输出日志，记录用户使用情况等
<!--more-->
```
 public static Logger logger;
private void initLogger()
        {
            // Step 1. Create configuration object
            LoggingConfiguration logConfig = new LoggingConfiguration();

            // Step 2. Create targets and add them to the configuration
            RichTextBoxTarget rtbTarget = new RichTextBoxTarget();
            logConfig.AddTarget("richTextBox", rtbTarget);
            rtbTarget.FormName = "MainForm"; // your winform class name
            rtbTarget.ControlName = "richTextBox1"; // your RichTextBox control/variable name
            //rtbTarget.UseDefaultRowColoringRules = false;
            rtbTarget.RowColoringRules.Add(
                new RichTextBoxRowColoringRule(
                    "level == LogLevel.Error", //condition
                    "DarkRed", // font color
                    "Control", //backgroud color
                    FontStyle.Bold
                    )
                );
            rtbTarget.RowColoringRules.Add(
                new RichTextBoxRowColoringRule(
                    "level == LogLevel.Warn", //condition
                    "Navy", // font color
                    "Control", //backgroud color
                    FontStyle.Bold
                    )
                );

            FileTarget fileTarget = new FileTarget();
            logConfig.AddTarget("logFile", fileTarget);

            // Step 3. Set target properties
            //string commonLayout = "${date:format=MM-dd HH\\:mm\\:ss} ${logger} ${message}";
            string commonLayout = "${date:format=HH\\:mm} ${logger} ${message}";
            rtbTarget.Layout = commonLayout;

            //string curDatetimeStr = DateTime.Now.ToString();
            DateTime curDateTime = DateTime.Now;
            string curDatetimeStr = String.Format("{0:yyyy-MM-dd_HHmmss}", curDateTime); //"2013-06-11_142102"
            fileTarget.FileName = "${basedir}/logfile/" + curDatetimeStr + "_log.txt"; //{'${basedir}/2013-06-11_142102_log.txt'}
            fileTarget.Layout = commonLayout;

            // Step 4. Define rules
            LoggingRule ruleRichTextBox = new LoggingRule("*", LogLevel.Debug, rtbTarget);
            logConfig.LoggingRules.Add(ruleRichTextBox);

            LoggingRule ruleFile = new LoggingRule("*", LogLevel.Debug, fileTarget);
            logConfig.LoggingRules.Add(ruleFile);

            // Step 5. Activate the configuration
            LogManager.Configuration = logConfig;

            // Example usage
            logger = LogManager.GetLogger("");
        }
//使用
 logger.Info("Info:开始进行文件夹目录结构检查");
 logger.Error("XXXXXXX");
```
