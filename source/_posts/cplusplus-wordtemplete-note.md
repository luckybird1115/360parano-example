---
title: C#读取word模板填充数据生成带水印的pdf
date: 2019-04-10 09:32:08
tags:
    - C# 
categories: C#
---
使用Microsoft Office 的COM组件,方便快捷，较好的复用和使用模板快速生成较复杂的文档  
不足在于对于环境依赖较高
<!--more-->
# Word相关处理类
```
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Linq;
using System.Text;
using Microsoft.Office.Interop.Word;
using System.Windows.Forms;
using System.Threading;
namespace GDZLCHECK.Utils
{
    class CreateWord
    {
        private _Application wordApp= null;
        private _Document wordDoc= null;
         public _Application Application
         {
            get
            {
            return wordApp;
            }
            set
            {
            wordApp = value;
            }
        }
        public _Document Document
        {
          get
          {
            return wordDoc;
          }
          set
          {
            wordDoc = value;
          }
        }

         //保存新文件
        public void SaveDocument(string filePath)
        {
           // killWinWordProcess();
            object oMissing = System.Reflection.Missing.Value;
            //SaveFileDialog sfd = new SaveFileDialog();
            //sfd.Filter = "Word Document(*.doc)|*.doc|PDF(*.pdf)|*.pdf";
            //sfd.DefaultExt = "Word Document(*.doc)|*.doc";
            //if (sfd.ShowDialog() == DialogResult.OK)
            //{
                //object filename = sfd.FileName;
            object filename = filePath;
            wordDoc.SaveAs(ref filename, ref oMissing, ref oMissing, ref oMissing,
            ref oMissing, ref oMissing, ref oMissing, ref oMissing, ref oMissing,
            ref oMissing, ref oMissing, ref oMissing, ref oMissing, ref oMissing,
            ref oMissing, ref oMissing);
            wordDoc.Close(ref oMissing, ref oMissing, ref oMissing);
            //关闭word
            wordApp.Quit(ref oMissing, ref oMissing, ref oMissing);
            //}
        }

        //在书签处插入值
        public bool InsertValue(string bookmark, string value)
        {
          object bkObj =bookmark;
          if(wordApp.ActiveDocument.Bookmarks.Exists(bookmark))
          {
            wordApp.ActiveDocument.Bookmarks.get_Item(ref bkObj).Select();
            wordApp.Selection.TypeText(value);
            return true;
          }
          return false;
        }
        
        //插入表格,bookmark书签
        public Table InsertTable(string bookmark, int rows, int columns,float width)
        {
          object miss =System.Reflection.Missing.Value;
          object oStart =bookmark;
          Range range =wordDoc.Bookmarks.get_Item(ref oStart).Range;//表格插入位置
          Table newTable =wordDoc.Tables.Add(range,rows, columns, ref miss, ref miss);
          //设置表的格式
          newTable.Borders.Enable =1; //允许有边框，默认没有边框(为0时报错，1为实线边框，2、3为虚线边框，以后的数字没试过)
          newTable.Borders.OutsideLineWidth=WdLineWidth.wdLineWidth050pt;//边框宽度
          if(width != 0)
          {
            newTable.PreferredWidth=width;//表格宽度
          }
          newTable.AllowPageBreaks =false;
          return newTable;
        }
        // 合并单元格 表id,开始行号,开始列号,结束行号,结束列号
        public void MergeCell(int n, int row1, int column1, int row2, int column2)
        {
            wordDoc.Content.Tables[n].Cell(row1, column1).Merge(wordDoc.Content.Tables[n].Cell(row2, column2));
        }

        // 合并单元格 表名,开始行号,开始列号,结束行号,结束列号
        public void MergeCell(Microsoft.Office.Interop.Word.Table table, int row1, int column1, int row2, int column2)
        {
            table.Cell(row1, column1).Merge(table.Cell(row2, column2));
        }

        // 设置表格内容对齐方式 Align水平方向，Vertical垂直方向(左对齐，居中对齐，右对齐分别对应Align和Vertical的值为-1,0,1)Microsoft.Office.Interop.Word.Table table
        public void SetParagraph_Table(int n, int Align, int Vertical)
        {
            switch (Align)
            {
                case -1: wordDoc.Content.Tables[n].Range.ParagraphFormat.Alignment = WdParagraphAlignment.wdAlignParagraphLeft; break;//左对齐
                case 0: wordDoc.Content.Tables[n].Range.ParagraphFormat.Alignment = WdParagraphAlignment.wdAlignParagraphCenter; break;//水平居中
                case 1: wordDoc.Content.Tables[n].Range.ParagraphFormat.Alignment = WdParagraphAlignment.wdAlignParagraphRight; break;//右对齐
            }
            switch (Vertical)
            {
                case -1: wordDoc.Content.Tables[n].Range.Cells.VerticalAlignment = WdCellVerticalAlignment.wdCellAlignVerticalTop; break;//顶端对齐
                case 0: wordDoc.Content.Tables[n].Range.Cells.VerticalAlignment = WdCellVerticalAlignment.wdCellAlignVerticalCenter; break;//垂直居中
                case 1: wordDoc.Content.Tables[n].Range.Cells.VerticalAlignment = WdCellVerticalAlignment.wdCellAlignVerticalBottom; break;//底端对齐
            }
        }

        // 设置单元格内容对齐方式
        public void SetParagraph_Table(int n, int row, int column, int Align, int Vertical)
        {
            switch (Align)
            {
                case -1: wordDoc.Content.Tables[n].Cell(row, column).Range.ParagraphFormat.Alignment = WdParagraphAlignment.wdAlignParagraphLeft; break;//左对齐
                case 0: wordDoc.Content.Tables[n].Cell(row, column).Range.ParagraphFormat.Alignment = WdParagraphAlignment.wdAlignParagraphCenter; break;//水平居中
                case 1: wordDoc.Content.Tables[n].Cell(row, column).Range.ParagraphFormat.Alignment = WdParagraphAlignment.wdAlignParagraphRight; break;//右对齐
            }
            switch (Vertical)
            {
                case -1: wordDoc.Content.Tables[n].Cell(row, column).Range.Cells.VerticalAlignment = WdCellVerticalAlignment.wdCellAlignVerticalTop; break;//顶端对齐
                case 0: wordDoc.Content.Tables[n].Cell(row, column).Range.Cells.VerticalAlignment = WdCellVerticalAlignment.wdCellAlignVerticalCenter; break;//垂直居中
                case 1: wordDoc.Content.Tables[n].Cell(row, column).Range.Cells.VerticalAlignment = WdCellVerticalAlignment.wdCellAlignVerticalBottom; break;//底端对齐
            }

        }


        // 设置表格字体
        public void SetFont_Table(Microsoft.Office.Interop.Word.Table table, string fontName, double size)
        {
            if (size != 0)
            {
                table.Range.Font.Size = Convert.ToSingle(size);
            }
            if (fontName != "")
            {
                table.Range.Font.Name = fontName;
            }
        }

        // 设置单元格字体
        public void SetFont_Table(int n, int row, int column, string fontName, double size, int bold)
        {
            if (size != 0)
            {
                wordDoc.Content.Tables[n].Cell(row, column).Range.Font.Size = Convert.ToSingle(size);
            }
            if (fontName != "")
            {
                wordDoc.Content.Tables[n].Cell(row, column).Range.Font.Name = fontName;
            }
            wordDoc.Content.Tables[n].Cell(row, column).Range.Font.Bold = bold;// 0 表示不是粗体，其他值都是
        }

        // 是否使用边框,n表格的序号,use是或否
        // 该处边框参数可以用int代替bool可以让方法更全面
        // 具体值方法中介绍
        public void UseBorder(int n, bool use)
        {
            if (use)
            {
                wordDoc.Content.Tables[n].Borders.Enable = 1;
                //允许有边框，默认没有边框(为0时无边框，1为实线边框，2、3为虚线边框，以后的数字没试过)
            }
            else
            {
                wordDoc.Content.Tables[n].Borders.Enable = 0;
            }
        }

        // 给表格插入一行,n表格的序号从1开始记
        public void AddRow(int n)
        {
            object miss = System.Reflection.Missing.Value;
            wordDoc.Content.Tables[n].Rows.Add(ref miss);
        }

        // 给表格添加一行
        public void AddRow(Microsoft.Office.Interop.Word.Table table)
        {
            object miss = System.Reflection.Missing.Value;
            table.Rows.Add(ref miss);
        }

        // 给表格插入rows行,n为表格的序号
        public void AddRow(int n, int rows)
        {
            object miss = System.Reflection.Missing.Value;
            Microsoft.Office.Interop.Word.Table table = wordDoc.Content.Tables[n];
            for (int i = 0; i < rows; i++)
            {
                table.Rows.Add(ref miss);
            }
        }

        // 删除表格第rows行,n为表格的序号
        public void DeleteRow(int n, int row)
        {
            Microsoft.Office.Interop.Word.Table table = wordDoc.Content.Tables[n];
            table.Rows[row].Delete();
        }

        // 给表格中单元格插入元素，table所在表格，row行号，column列号，value插入的元素
        public void InsertCell(Microsoft.Office.Interop.Word.Table table, int row, int column, string value)
        {
            table.Cell(row, column).Range.Text = value;
        }

        // 给表格中单元格插入元素，n表格的序号从1开始记，row行号，column列号，value插入的元素
        public void InsertCell(int n, int row, int column, string value)
        {
            wordDoc.Content.Tables[n].Cell(row, column).Range.Text = value;
        }

        // 给表格插入一行数据，n为表格的序号，row行号，columns列数，values插入的值
        public void InsertCell(int n, int row, int columns, string[] values)
        {
            Microsoft.Office.Interop.Word.Table table = wordDoc.Content.Tables[n];
            for (int i = 0; i < columns; i++)
            {
                table.Cell(row, i + 1).Range.Text = values[i];
            }
        }
        //杀掉winword.exe进程
        public void killWinWordProcess()
        {
          System.Diagnostics.Process[] processes = System.Diagnostics.Process.GetProcessesByName("WINWORD");
          foreach (System.Diagnostics.Process process in processes)
          {
            bool b = process.MainWindowTitle=="";
            if (process.MainWindowTitle =="")
            {
              process.Kill();
            }
          }
        }

    }
}

```

# Word转pdf
```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using Microsoft.Office.Interop.Word;
using System.IO;
namespace GDZLCHECK
{
    class WordToPdfUtil
    {
        public static bool wordToPdf(String sourcePath)
        {
            bool result = false;
            _Application application = new Application();
            _Document document = null;
            try
            {
                application.Visible = false;
                document = application.Documents.Open(sourcePath);
                string PDFPath = sourcePath.Replace(".doc", ".pdf");//pdf存放位置
                if (!File.Exists(@PDFPath))//存在PDF，不需要继续转换
                {
                    document.ExportAsFixedFormat(PDFPath, Microsoft.Office.Interop.Word.WdExportFormat.wdExportFormatPDF);
                }
                result = true;
            }
            catch (Exception e)
            {
                Console.WriteLine(e.Message);
                result = false;
            }
            finally
            {
                document.Close();
            }
            return result;
        }
    }
}

```

# pdf加水印
```
using iTextSharp.text;
using iTextSharp.text.pdf;
using System;
using System.Collections.Generic;
using System.Data;
using System.IO;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows.Forms;
 public static void setWatermark(string filePath, string outputfilepath, string waterMarkName, string waterMarkAddr)
        {
            PdfReader pdfReader = null;
            PdfStamper pdfStamper = null;
            try
            {
                pdfReader = new PdfReader(filePath);
                pdfStamper = new PdfStamper(pdfReader, new FileStream(outputfilepath, FileMode.Create));
                //设置权限为只读
                pdfStamper.SetEncryption(PdfWriter.STRENGTH128BITS, null, null, PdfWriter.ALLOW_SCREENREADERS | PdfWriter.AllowPrinting);                         
                int total = pdfReader.NumberOfPages+1;//获取PDF的总页数
                iTextSharp.text.Rectangle psize = pdfReader.GetPageSize(1);//获取第一页
                float width = psize.Width;//PDF页面的宽度，用于计算水印倾斜
                float height = psize.Height;
                PdfContentByte waterContent;
                BaseFont basefont = BaseFont.CreateFont(@"C:\WINDOWS\Fonts\SIMFANG.TTF", BaseFont.IDENTITY_H, BaseFont.EMBEDDED);
                PdfGState gs = new PdfGState();
                //i从2开始，因为不给封面添加水印
                for (int i = 2; i < total; i++)
                {
                    waterContent = pdfStamper.GetOverContent(i);//在内容上方加水印
                    //透明度
                    waterContent.SetGState(gs);
                    //开始写入文本
                    waterContent.BeginText();
                    waterContent.SetColorFill(BaseColor.RED);
                    waterContent.SetFontAndSize(basefont, 30);
                    waterContent.SetTextMatrix(0, 0);
                    if (waterMarkAddr == null || waterMarkAddr == "")
                    {
                        waterContent.ShowTextAligned(Element.ALIGN_CENTER, waterMarkName, width / 2, height / 2, 55);
                    }
                    else
                    {
                        waterContent.ShowTextAligned(Element.ALIGN_CENTER, waterMarkName, width / 2, height / 2 + 100, 55);
                        waterContent.ShowTextAligned(Element.ALIGN_CENTER, waterMarkAddr, width / 2, height / 2 - 100, 55);
                    }
                    waterContent.EndText();
                }
            }
            catch (Exception e)
            {
                Console.WriteLine(e.ToString());
            }
            finally
            {
                if (pdfStamper != null)
                    pdfStamper.Close();

                if (pdfReader != null)
                    pdfReader.Close();
            }
        }
        
```

# 主函数调用
```
 if (this.tbManagerName.Text.ToString() == "" || this.tbContact.Text.ToString() == "" || this.tbUnit.Text.ToString() == "")
            {
                MessageBox.Show("请输入项目负责人姓名,联系电话,承担单位");
                return;
            }         
            //ConstantInfo.outStatic();
            FolderBrowserDialog dialog = new FolderBrowserDialog();
            dialog.Description = "请选择检查报告输出位置";
            string outputFilePath = "";
            if (dialog.ShowDialog() == DialogResult.OK)
            {
                outputFilePath = dialog.SelectedPath;
                string managerName = this.tbManagerName.Text.ToString();
                string contactNum = this.tbContact.Text.ToString();
                string unitName = this.tbUnit.Text.ToString();
                //水印1
                //PdfUtils.GeneratePDF(resultDt, strPacName, isPassed, managerName, contactNum, unitName, outputFilePath, 1);
                //水印2
                // PdfUtils.GeneratePDF(resultDt, strPac,isPassed,outputFilePath, 2);
                //图片水印 
                // string picPath = string.Format("{0}/Resource/{1}", AppDomain.CurrentDomain.BaseDirectory,"logo.png");
                //PdfUtils.GeneratePDF(resultDt, strPac,isPassed,outputFilePath, 3,picPath);  
                //读取word模板生成数据，转为PDF，然后添加水印，删除临时文件
                this.lbShowProcess.Text = "正在准备统计数据...";
                this.pbProgress.Visible = true;
                statisticNum();
                this.pbProgress.Position = 30; 
                this.lbShowProcess.Text = "开始生成检查报告...";
                           
                savePdf(outputFilePath, managerName, contactNum, unitName);
                this.lbShowProcess.Text = "检查报告导出完成";
            }
            
 //保存pdf
   private void savePdf(string outputFilePath, string managerName, string contactNum, string unitName)
          {
              String[] origin = { "O1", "O2", "O3", "O4", "O5", "O6" };//设计点             
              //创建一个Word应用程序实例
              Microsoft.Office.Interop.Word._Application oWord = new Microsoft.Office.Interop.Word.Application();
              //设置为不可见
              oWord.Visible = false;
              //模板文件地址，这里假设在X盘根目录
              string templatePath = string.Format("{0}/Config/{1}", AppDomain.CurrentDomain.BaseDirectory, "template.dot");
              object oTemplate = templatePath;
              //以模板为基础生成文档
              Microsoft.Office.Interop.Word._Document oDoc = oWord.Documents.Add(ref oTemplate, ref oMissing, ref oMissing, ref oMissing);
  
              CreateWord c = new CreateWord();
              c.Application = oWord;
  
              c.Document = oDoc;
             int total = origin.Length
             this.pbProgress.Properties.Minimum = 0;            
              this.pbProgress.Properties.Maximum =total+10;
              this.pbProgress.Properties.Step = 1;                             
              int i = 0, j = 0;
              foreach (String s in origin)
              { 
                  Application.DoEvents();
                  c.InsertValue(s, Convert.ToString(ConstantInfo.StaticResult[j][i]));
                  this.pbProgress.PerformStep();
                  i++;
              }            
              c.InsertValue("单位", unitName);
              c.InsertValue("负责人", managerName);
              c.InsertValue("电话", contactNum);
              c.InsertValue("日期", DateTime.Now.ToString("yyyy-MM-dd HH:mm:ss"));                    
              int rowNum = resultDt.Rows.Count;
              int colNum = resultDt.Columns.Count;
              if (rowNum > 0)
              {
                  //插入结果行
                  c.AddRow(4, rowNum);
                  //插入值
                  for (int rowIndex = 0; rowIndex < rowNum; rowIndex++)
                  {
                      Application.DoEvents();
                      for (int colIndex = 0; colIndex < colNum; colIndex++)
                      {
                          c.InsertCell(4, rowIndex+2, colIndex+1, resultDt.Rows[rowIndex][colIndex].ToString());
                      }
                      this.pbProgress.PerformStep();
                  }
  
              }
  
              string fileName = string.Format("数据_{0}.doc", DateTime.Now.ToString("yyyyMMddHHmmss"));
              string filePath = string.Format("{0}/Resource/{1}", AppDomain.CurrentDomain.BaseDirectory, fileName);
              c.SaveDocument(filePath);//此时已经生成word
              Thread.Sleep(5000);
              WordToPdfUtil.wordToPdf(filePath);//转为pdf
  
              string waterMarkName = "检查:" + (isPassed ? "通过" : "未通过");
              string waterMarkAddr = strPacName;
              string outputFileName = string.Format(this.strPacName+"数据入库预检查报告_{0}.pdf", DateTime.Now.ToString("yyyyMMddHHmmss"));
              outputFilePath = string.Format("{0}/{1}", outputFilePath, outputFileName);
              if (outputFilePath == null || outputFilePath == "")
              {
                  outputFilePath = string.Format("{0}/Resource/{1}", AppDomain.CurrentDomain.BaseDirectory, outputFileName);
              }
              string pdfPath = filePath.Replace(".doc", ".pdf");//pdf存放位置
              setWatermark(pdfPath, outputFilePath, waterMarkName, waterMarkAddr);//先生成水印，再删除临时文件 ,加密在设置水印中实现
  
              //先生成水印，再删除临时文件
              if (File.Exists(filePath))//判断临时文件是否存在，如果存在则删除
              {
                  File.Delete(filePath);
                  GC.Collect();//回收垃圾
              }
              string PDFPath = filePath.Replace(".doc", ".pdf");//pdf存放位置
              if (File.Exists(PDFPath))//判断临时文件是否存在，如果存在则删除
              {
                  File.Delete(PDFPath);
                  GC.Collect();//回收垃圾
              }
              Application.DoEvents();
              this.pbProgress.Position = total + 10;
              MessageBox.Show("保存检查结果成功");
              this.pbProgress.Visible = false;
              Console.WriteLine("done");
          }          
```
