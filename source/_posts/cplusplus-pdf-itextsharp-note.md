---
title: C#利用itextSharp直接生成pdf
date: 2019-04-10 09:16:23
tags:
    - VS打包
    - C#
categories: C#
---
C#使用开源第三方库itextsharp直接生成pdf,相较于Microsoft Office 的COM组件，不需要依赖office环境，程序兼容性好，更轻量。  
但也存在一些不足，对于复杂模板的实现较为复杂，介绍一个使用实例
<!--more-->
# pdf工具类
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

namespace GDZLCHECK.Utils
{
    class PdfUtils
    {
        private static PdfUtils instance;
        public static PdfUtils GetInstance()
        {
            if (instance == null)
            {
                instance = new PdfUtils();
            }
            return instance;
        }

        private static Document doc;
        private static BaseFont bf = BaseFont.CreateFont(@"C://Windows/Fonts/simsun.ttc,0", BaseFont.IDENTITY_H, BaseFont.EMBEDDED);
        //四种字体
        private static Font fontBig = new Font(bf, 20, Font.BOLD);
        private static Font fontMiddle = new Font(bf, 15, Font.BOLD);
        private static Font fontSmall = new Font(bf, 13, Font.BOLD);
        private static Font fontSmall1 = new Font(bf, 13, Font.BOLD);
        private static Font fontSmallNoBold = new Font(bf, 13);
        private static float IndentationLeft = 50;//距左边距
        //如果要传参数进来，可自定义
        public static void GeneratePDF(DataTable tableSource, string pac, bool isPassed,string managerName,string contactNum,string unitName,string outputFilePath = "", int type = 1, string watermarkPicPath = "")
        {
            doc = new Document(PageSize.A4);
            doc.AddAuthor("XXXXXX");
            doc.AddCreationDate();
            doc.AddHeader("isPass", isPassed.ToString());
            try
            {
                //MemoryStream ms2 = new MemoryStream();
                string fileName = string.Format("数据_{0}.pdf", DateTime.Now.ToString("yyyyMMddHHmmss"));
                string filePath = string.Format("{0}/Resource/{1}", AppDomain.CurrentDomain.BaseDirectory, fileName);
                FileStream fs = new FileStream(filePath, FileMode.Create);//创建临时文件，到时生成好后删除
                PdfWriter writer = PdfWriter.GetInstance(doc, fs);
                writer.CloseStream = false;//把doc内容写入流中
                doc.Open();
                //添加封面
                //添加质检标志
                string picPath = string.Format("{0}/Resource/{1}", AppDomain.CurrentDomain.BaseDirectory, "mark.png");
                Image img = Image.GetInstance(new Uri(picPath));
                float PageHeight = PageSize.A4.Height/7;
                double percentHeight = Math.Round((PageHeight / img.Height), 2);

                float PageWidth = PageSize.A4.Width/2;
                double percentWidth = Math.Round((PageWidth / img.Width), 2);
                double percent = percentHeight > percentWidth ? percentWidth : percentHeight;
                img.ScalePercent((float)percent * 100);
                doc.Add(img);
                //每次在添加文本内容之前可以先设置字体
                HeaderAndFooterEvent.SetFont(BaseColor.BLACK, "宋体", 36, Font.BOLD);
                //添加一个空段落来占位，五个参数分别为：内容，对齐方式（1为居中，0为居左，2为居右），段后空行数，段前空行数，行间距
                //doc.Add(HeaderAndFooterEvent.AddParagraph(" ", 1, 100, 0, 1.5f));

                //这个方法为上一个方法的重载，三个参数分别为：内容，对齐方式，行间距
                doc.Add(HeaderAndFooterEvent.AddParagraph("检  验  报  告", 1, 2.41f));
                HeaderAndFooterEvent.SetFont(BaseColor.BLACK, "宋体", 28, Font.BOLD);
                doc.Add(HeaderAndFooterEvent.AddParagraph("TEST  REPORT", 1, 1.5f));
                doc.Add(HeaderAndFooterEvent.AddParagraph(" ", 1, 50, 0, 1.5f));
                HeaderAndFooterEvent.SetFont(BaseColor.BLACK, "宋体", 28, Font.BOLD);//空行
                string projectName="项目名称";
                //检测信息             
                bool isChangeRow = false;//对于项目名称过长需要换行截断
                string anotherStr = "";
                if (projectName.Length > 19)
                {
                    isChangeRow = true;
                    anotherStr = projectName.Substring(19, projectName.Length - 19);
                }
                if (isChangeRow)
                {
                    AddPartnerContent("项目名称  :", projectName.Substring(0, 19), 38, 0);//添加项目名称
                    AddPartnerContent("           ", anotherStr, 38, 0);//添加项目名称

                }
                else
                {
                    AddPartnerContent("项目名称  :", projectName, 38, 0);//添加项目名称
                }

                AddPartnerContent("项目负责人:", managerName, 38, 0);//添加项目负责人
                AddPartnerContent("联系电话  :", contactNum, 38, 1);//添加联系电话
                AddPartnerContent("承担单位  :", unitName, 38, 0);//添加承担单位
                AddPartnerContent("检测日期  :", DateTime.Now.ToString("yyyy-MM-dd HH:mm:ss"), 38, 1);//添加项目名称
                //检查结果详情
                doc.NewPage();
                //实现页眉页脚  
                writer.PageEvent = new HeaderAndFooterEvent();
                HeaderAndFooterEvent.PAGE_NUMBER = true;//实现页眉跟页脚  
                HeaderAndFooterEvent.tpl = writer.DirectContent.CreateTemplate(500, 500); //定义模板

                HeaderAndFooterEvent.HeaderLeft = "耕地质量地球化学调查评价";
                HeaderAndFooterEvent.HeaderRight = "成果检查";
                HeaderAndFooterEvent.FooterLeft = "";
                HeaderAndFooterEvent.FooterRight = DateTime.Now.ToString("yyyy-MM-dd HH:mm:ss") + "(UTC)";
                //核心操作
                CreateLine();//生成一条下横线
                CreateEmptyRow(1);//生成一行空行
                HeaderAndFooterEvent.SetFont(BaseColor.BLACK, "宋体", 15, Font.BOLD);
                doc.Add(HeaderAndFooterEvent.AddParagraph("耕地质量地球化学调查评价成果检查报告", 1, 1.5f));
                //AddHeaderTitleContent("汇交数据检查报告");//添加表头
                CreateEmptyRow(1);//生成一行空行
                //AddPartnerContent("生产单位", pac, 30);//添加合作单位
                //AddPartnerContent("检查单位","WHU",25);//添加合作单位
                //AddPageNumberContent(1,1);//添加页码
                HeaderAndFooterEvent.SetFont(BaseColor.BLACK, "宋体1", 12);
                doc.Add(HeaderAndFooterEvent.AddParagraph("项目名称:  XXXXXX", 0, 1.5f));
                doc.Add(HeaderAndFooterEvent.AddParagraph("项目负责人:"+managerName+"              ", 0, 1.5f));
                doc.Add(HeaderAndFooterEvent.AddParagraph("联系电话:"+contactNum+"         ", 0, 1.5f));
                doc.Add(HeaderAndFooterEvent.AddParagraph("承担单位:" + unitName + "          ", 0, 1.5f));
                doc.Add(HeaderAndFooterEvent.AddParagraph("检测日期: " + DateTime.Now.ToString("yyyy-MM-dd HH:mm:ss"), 0, 1.5f));
                CreateEmptyRow(1);//生成一行空行

                #region 生成表格数据
                PdfPTable table = new PdfPTable(6);//5列的table
                //添加表格列头     
                table.WidthPercentage = 100;
                table.SetTotalWidth(new float[] { 70, 150,80, 150, 80, 120 });
                table.AddCell(GetPdfCell("序号", fontSmallNoBold, Element.ALIGN_CENTER));
                table.AddCell(GetPdfCell("检查对象", fontSmallNoBold, Element.ALIGN_CENTER));
                table.AddCell(GetPdfCell("检查类型", fontSmallNoBold, Element.ALIGN_CENTER));
                table.AddCell(GetPdfCell("检查内容", fontSmallNoBold, Element.ALIGN_CENTER));
                table.AddCell(GetPdfCell("检查结果", fontSmallNoBold, Element.ALIGN_CENTER));
                table.AddCell(GetPdfCell("备注", fontSmallNoBold, Element.ALIGN_CENTER));              

                int emptyRow = 20;//如果table的行数小于20行，那么剩余部分显示空白行
                if (tableSource.Rows.Count > 0)
                {
                    emptyRow = emptyRow - tableSource.Rows.Count;//如果为负数，说明不需要生成空白行
                    for (int i = 0; i < tableSource.Rows.Count; i++)
                    {
                        DataRow row = tableSource.Rows[i];
                        table.AddCell(GetPdfCell((i + 1).ToString(), fontSmallNoBold, Element.ALIGN_CENTER));
                        table.AddCell(GetPdfCell(row["Object"].ToString(), fontSmallNoBold, Element.ALIGN_CENTER));
                        table.AddCell(GetPdfCell(row["Type"].ToString(), fontSmallNoBold, Element.ALIGN_CENTER));
                        table.AddCell(GetPdfCell(row["Content"].ToString(), fontSmallNoBold, Element.ALIGN_CENTER));
                        table.AddCell(GetPdfCell(row["Result"].ToString(), fontSmallNoBold, Element.ALIGN_CENTER));
                        table.AddCell(GetPdfCell(row["Info"].ToString(), fontSmallNoBold, Element.ALIGN_CENTER));                      
                    }
                }
                if (emptyRow > 0)//说明数据源不足20行
                {
                    for (int i = 0; i < emptyRow; i++)
                    {
                        table.AddCell(GetPdfCell(((20 - emptyRow) + i + 1).ToString(), fontSmallNoBold, Element.ALIGN_CENTER));
                        table.AddCell(GetPdfCell("", fontSmallNoBold, Element.ALIGN_CENTER));
                        table.AddCell(GetPdfCell("", fontSmallNoBold, Element.ALIGN_CENTER));
                        table.AddCell(GetPdfCell("", fontSmallNoBold, Element.ALIGN_CENTER));
                        table.AddCell(GetPdfCell("", fontSmallNoBold, Element.ALIGN_CENTER));                       
                    }
                }
                doc.Add(table);
                #endregion

                #region 添加水印
                string waterMarkName = "检查:"+(isPassed?"通过":"未通过");
                string waterMarkAddr = pac;              
                #endregion

                doc.Close();          
                fs.Close();
                string outputFileName = string.Format("检查报告_{0}.pdf", DateTime.Now.ToString("yyyyMMddHHmmss"));
                outputFilePath = string.Format("{0}/{1}", outputFilePath, outputFileName);
                if (outputFilePath == null || outputFilePath == "")
                {
                     outputFilePath = string.Format("{0}/Resource/{1}", AppDomain.CurrentDomain.BaseDirectory, outputFileName);
                }
                
                switch (type)
                {
                    case 1:
                        //目前使用并在其中设置了只读
                        setWatermark(filePath,outputFilePath,waterMarkName,waterMarkAddr);//先生成水印，再删除临时文件
                        break;
                    case 2:                       
                       waterMarkName = string.Format("汇交检查：{0}  区域：{1} ", "通过", waterMarkAddr);
                       setWatermark(filePath, outputFilePath, waterMarkName);
                        break;
                    case 3:
                        setPicWatermark(filePath, outputFilePath, watermarkPicPath);
                        break;

                }
                //先生成水印，再删除临时文件
                if (File.Exists(filePath))//判断临时文件是否存在，如果存在则删除
                {
                    File.Delete(filePath);
                    GC.Collect();//回收垃圾
                }
                //设置只读
                //pdfreadonly(outputFilePath);
                MessageBox.Show("保存检查结果成功");

            }
            catch (DocumentException ex)
            {
                throw new Exception(ex.Message);
            }
        }
        #region 生成一条横线
        private static void CreateLine()
        {
            PdfPTable table = new PdfPTable(1);//一个单元格的
            table.WidthPercentage = 100;
            PdfPCell cell = new PdfPCell();
            cell.BorderWidthBottom = 0.5f;
            table.AddCell(cell);
            doc.Add(table);
        }
        #endregion

        #region 生成N行空白行
        private static void CreateEmptyRow(int emptyRowNum)
        {
            for (int i = 0; i < emptyRowNum; i++)
            {
                doc.Add(new Paragraph(" "));
            }
        }
        #endregion

        #region 生成标题
        private static void AddHeaderTitleContent(string content)
        {
            Paragraph p = new Paragraph(content, fontMiddle);
            p.IndentationLeft = IndentationLeft;//距离左边距
            doc.Add(p);
        }
        #endregion

        #region 生成带下划线的内容
        private static void AddPartnerContent(string stationName, string stationValue, int maxlength,int type)
        {
            fontSmall.SetStyle(Font.UNDERLINE);//文字下划线          
            Paragraph content = new Paragraph();
            content.IndentationLeft = 100;
            Chunk chunkName = new Chunk(stationName, fontSmall1);
            Chunk chunkText = new Chunk(GetEmptyString(maxlength, stationValue,type), fontSmall);
            content.Add(0, chunkName);
            content.Add(1, chunkText);
            content.SpacingAfter = 2;
            content.SpacingBefore = 2;
            content.MultipliedLeading = 1.5f;
            content.Alignment = 0;
            doc.Add(content);           
        }
        //居中显示内容
        private static string GetEmptyString(int maxlength, string text,int type)
        {
            int padding =0;
            if (type == 0)
            {
                padding = (maxlength - text.Length* 2) ;
            }
            else {
                padding = (maxlength - text.Length);
            }
            string empty = string.Empty;
            for (int i = 0; i < padding; i++)
            {
                empty += " ";
            }
            return string.Format("{1}{0}", empty, text);
        }
        #endregion

        #region 生成页码
        private static void AddPageNumberContent(int total,int currentPage)
        {
            var content = new Paragraph("共 "+total+" 页  第"+currentPage+"   页", fontSmall);
            content.IndentationRight = IndentationLeft + 20;
            content.Alignment = 2;    //居左
            doc.Add(content);
        }
        #endregion

        #region 生成单元格
        private static PdfPCell GetPdfCell(string content, Font font, int horizontalAlignment)
        {
            PdfPCell cell = new PdfPCell(new Paragraph(content, font));
            cell.HorizontalAlignment = horizontalAlignment;//水平位置
            cell.VerticalAlignment = Element.ALIGN_CENTER;//垂直居中
            cell.MinimumHeight = 20;//单元格的最小高度
            return cell;
        }
        #endregion

        #region 生成水印
     
        public static bool CreatePDFByPic(string picPdfPath, string picPath)
        {
            //新建一个文档
            Document doc = new Document();
            try
            {
                //建立一个书写器(Writer)与document对象关联
                PdfWriter.GetInstance(doc, new FileStream(picPdfPath, FileMode.Create, FileAccess.ReadWrite));
                //打开一个文档
                doc.Open();
                //向文档中添加内容
                Image img = Image.GetInstance(picPath);
                //img.SetAbsolutePosition();
                doc.Add(img);
                return true;
            }
            catch (Exception ex)
            {
                return false;
                throw ex;
            }
            finally
            {
                if (doc != null)
                {
                    doc.Close();
                }
            }

        }

        /// <summary>
        /// 加图片水印
        /// </summary>
        /// <param name="inputfilepath"></param>
        /// <param name="outputfilepath"></param>
        /// <param name="ModelPicName"></param>
        /// <param name="top"></param>
        /// <param name="left"></param>
        /// <returns></returns>
        public static bool setPicWatermark(string inputfilepath, string outputfilepath, string ModelPicName)
        {
            //throw new NotImplementedException();
            PdfReader pdfReader = null;
            PdfStamper pdfStamper = null;
            try
            {
                pdfReader = new PdfReader(inputfilepath);
                int numberOfPages = pdfReader.NumberOfPages;

                iTextSharp.text.Rectangle psize = pdfReader.GetPageSize(1);

                float width = psize.Width;

                float height = psize.Height;              
               

                pdfStamper = new PdfStamper(pdfReader, new FileStream(outputfilepath, FileMode.Create));

                PdfContentByte waterMarkContent;

                iTextSharp.text.Image image = iTextSharp.text.Image.GetInstance(ModelPicName);
                float left = width / 2 - image.Width; 
                float top = 10;
                image.GrayFill = 50;//透明度，灰色填充
                //image.Rotation//旋转
                //image.RotationDegrees//旋转角度
                //水印的位置 
                if (left < 0)
                {
                    left = width / 2 - image.Width;
                }

                //image.SetAbsolutePosition(left, (height - image.Height) - top);
                image.SetAbsolutePosition(left, (height / 2 - image.Height) - top);


                //每一页加水印,也可以设置某一页加水印 
                for (int i = 1; i <= numberOfPages; i++)
                {
                    //waterMarkContent = pdfStamper.GetUnderContent(i);//内容下层加水印
                    waterMarkContent = pdfStamper.GetOverContent(i);//内容上层加水印

                    waterMarkContent.AddImage(image);
                }
                //strMsg = "success";
                return true;
            }
            catch (Exception ex)
            {
                throw ex;

            }
            finally
            {

                if (pdfStamper != null)
                    pdfStamper.Close();

                if (pdfReader != null)
                    pdfReader.Close();
            }
        }
        /// <summary>
        /// 添加普通偏转角度文字水印
        /// </summary>
        /// <param name="inputfilepath"></param>
        /// <param name="outputfilepath"></param>
        /// <param name="waterMarkName"></param>
        /// <param name="permission"></param>
        public static void setWatermark(string inputfilepath, string outputfilepath, string waterMarkName)
        {
            PdfReader pdfReader = null;
            PdfStamper pdfStamper = null;
            try
            {
                pdfReader = new PdfReader(inputfilepath);
                pdfStamper = new PdfStamper(pdfReader, new FileStream(outputfilepath, FileMode.Create));
                int total = pdfReader.NumberOfPages + 1;
                iTextSharp.text.Rectangle psize = pdfReader.GetPageSize(1);
                float width = psize.Width;
                float height = psize.Height;
                PdfContentByte content;
                BaseFont font = BaseFont.CreateFont(@"C:\WINDOWS\Fonts\SIMFANG.TTF", BaseFont.IDENTITY_H, BaseFont.EMBEDDED);
                PdfGState gs = new PdfGState();
                for (int i = 1; i < total; i++)
                {
                    content = pdfStamper.GetOverContent(i);//在内容上方加水印
                    //content = pdfStamper.GetUnderContent(i);//在内容下方加水印
                    //透明度
                    gs.FillOpacity = 0.3f;
                    content.SetGState(gs);
                    //content.SetGrayFill(0.3f);
                    //开始写入文本
                    content.BeginText();
                    content.SetColorFill(BaseColor.LIGHT_GRAY);
                    content.SetFontAndSize(font, 40);
                    content.SetTextMatrix(0, 0);
                    content.ShowTextAligned(Element.ALIGN_CENTER, waterMarkName, width / 2 - 50, height / 2 - 50, 55);
                    //content.SetColorFill(BaseColor.BLACK);
                    //content.SetFontAndSize(font, 8);
                    //content.ShowTextAligned(Element.ALIGN_CENTER, waterMarkName, 0, 0, 0);
                    content.EndText();
                }
            }
            catch (Exception ex)
            {
                throw ex;
            }
            finally
            {

                if (pdfStamper != null)
                    pdfStamper.Close();

                if (pdfReader != null)
                    pdfReader.Close();
            }
        }
        /// <summary>
        /// 添加倾斜水印
        /// </summary>
        /// <param name="inputfilepath"></param>
        /// <param name="outputfilepath"></param>
        /// <param name="waterMarkName"></param>
        /// <param name="userPassWord"></param>
        /// <param name="ownerPassWord"></param>
        /// <param name="permission"></param>
        public static void setWatermark(string inputfilepath, string outputfilepath, string waterMarkName, string userPassWord, string ownerPassWord, int permission)
        {
            PdfReader pdfReader = null;
            PdfStamper pdfStamper = null;
            try
            {
                pdfReader = new PdfReader(inputfilepath);
                pdfStamper = new PdfStamper(pdfReader, new FileStream(outputfilepath, FileMode.Create));
                // 设置密码   
                //pdfStamper.SetEncryption(false,userPassWord, ownerPassWord, permission); 

                int total = pdfReader.NumberOfPages + 1;
                PdfContentByte content;
                BaseFont font = BaseFont.CreateFont(@"C:\WINDOWS\Fonts\SIMFANG.TTF", BaseFont.IDENTITY_H, BaseFont.EMBEDDED);
                PdfGState gs = new PdfGState();
                gs.FillOpacity = 0.2f;//透明度

                int j = waterMarkName.Length;
                char c;
                int rise = 0;
                for (int i = 1; i < total; i++)
                {
                    rise = 500;
                    content = pdfStamper.GetOverContent(i);//在内容上方加水印
                    //content = pdfStamper.GetUnderContent(i);//在内容下方加水印

                    content.BeginText();
                    content.SetColorFill(BaseColor.DARK_GRAY);
                    content.SetFontAndSize(font, 50);
                    // 设置水印文字字体倾斜 开始 
                    if (j >= 15)
                    {
                        content.SetTextMatrix(200, 120);
                        for (int k = 0; k < j; k++)
                        {
                            content.SetTextRise(rise);
                            c = waterMarkName[k];
                            content.ShowText(c + "");
                            rise -= 20;
                        }
                    }
                    else
                    {
                        content.SetTextMatrix(180, 100);
                        for (int k = 0; k < j; k++)
                        {
                            content.SetTextRise(rise);
                            c = waterMarkName[k];
                            content.ShowText(c + "");
                            rise -= 18;
                        }
                    }
                    // 字体设置结束 
                    content.EndText();
                    // 画一个圆 
                    //content.Ellipse(250, 450, 350, 550);
                    //content.SetLineWidth(1f);
                    //content.Stroke(); 
                }

            }
            catch (Exception ex)
            {
                throw ex;
            }
            finally
            {

                if (pdfStamper != null)
                    pdfStamper.Close();

                if (pdfReader != null)
                    pdfReader.Close();
            }
        }
       /// <summary>
       /// 
       /// </summary>
       /// <param name="filePath"></param>
       /// <param name="outputfilepath"></param>
       /// <param name="waterMarkName"></param>
       /// <param name="waterMarkAddr"></param>
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
        #endregion
        #region 只读
        public static void pdfreadonly(string pdfname)
        {

            string pdfFile = pdfname;// "d:\\sample.pdf";



            //把文件读入内存可立即释放文件句柄，方便信息覆盖原文件

            PdfReader reader = new PdfReader(File.ReadAllBytes(pdfFile));



            //获取到本文件的 MetaData 信息

            Dictionary<string, string> info = reader.Info;



            reader.Close();



            //覆盖掉原 PDF 文件中

            PdfStamper stamper = new PdfStamper(reader, new FileStream(pdfFile, FileMode.Create, FileAccess.Write));



            stamper.MoreInfo = info;

            stamper.SetEncryption(PdfWriter.STRENGTH40BITS, null, null, PdfWriter.AllowScreenReaders);



            stamper.Close();

        }
        #endregion
    }
}

```

# 页眉页脚生成类
```
using iTextSharp.text;
using iTextSharp.text.pdf;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace GDZLCHECK.Utils
{
    public class HeaderAndFooterEvent : PdfPageEventHelper, IPdfPageEvent
    {
        #region 静态字段

        public static PdfTemplate tpl = null;
        public static bool PAGE_NUMBER = false;//为True时就生成 页眉和页脚  
        public static iTextSharp.text.Rectangle rect = PageSize.A4;   //文档大小

        /// <summary>
        /// 正文字体
        /// </summary>
        private static Font font;

        /// <summary>
        /// 页眉页脚字体
        /// </summary>
        public static string HeaderFooterFontName = "黑体";

        /// <summary>
        /// 页头页脚字号
        /// </summary>
        public static int HeaderFooterFontSize = 10;

        /// <summary>
        /// 页头页尾字体颜色
        /// </summary>
        public static BaseColor HeaderFooterFontColor = BaseColor.BLACK;

        /// <summary>
        /// 左边页眉
        /// </summary>
        public static string HeaderLeft { get; set; }
        /// <summary>
        /// 右边页眉
        /// </summary>
        public static string HeaderRight { get; set; }
        /// <summary>
        /// 左边页脚
        /// </summary>
        public static string FooterLeft { get; set; }
        /// <summary>
        /// 右边页脚
        /// </summary>
        public static string FooterRight { get; set; }

        #endregion

        #region 设置页面大小
        /// <summary>
        /// 设置页面大小
        /// </summary>
        /// <param name="type">页面大小(如"A4")</param>
        public static void SetPageSize(string type)
        {
            switch (type.Trim())
            {
                case "A4":
                    rect = PageSize.A4;
                    break;
                case "A8":
                    rect = PageSize.A8;
                    break;
            }
        }
        #endregion

        #region 设置字体
        /// <summary>
        /// 设置字体
        /// </summary>
        /// <param name="size">字体大小</param>
        public static void SetFont(BaseColor color, string fontName = "华文中宋", float size = 12, int style = Font.NORMAL)
        {
            font = new Font(BaseFontAndSize(fontName), size, style, color);
        }
        #endregion

        #region 生成页眉页脚

        /// <summary>
        /// 关闭一个页面时发生  
        /// </summary>
        public override void OnEndPage(PdfWriter writer, Document document)
        {
            if (PAGE_NUMBER)
            {
                Font HeaderFooterFont = FontAndSize(HeaderFooterFontName, HeaderFooterFontSize, Font.NORMAL, HeaderFooterFontColor);
                Phrase header_left = new Phrase(HeaderLeft, HeaderFooterFont);
                Phrase header_right = new Phrase(HeaderRight, HeaderFooterFont);

                Phrase footer_left = new Phrase(FooterLeft, HeaderFooterFont);
                Phrase footer_center = new Phrase("第" + (writer.PageNumber-1) + "页/共     页", HeaderFooterFont);
                Phrase footer_right = new Phrase(FooterRight, HeaderFooterFont);



                PdfContentByte cb = writer.DirectContent;

                //模版 显示总共页数  
                cb.AddTemplate(tpl, document.Right - 290 + document.LeftMargin, document.Bottom - 15);//调节模版显示的位置  

                //页眉显示的位置  
                ColumnText.ShowTextAligned(cb, Element.ALIGN_CENTER, header_right,
                       document.Right - 50 + document.LeftMargin, document.Top + 15, 0);

                ColumnText.ShowTextAligned(cb, Element.ALIGN_CENTER, header_left,
                       document.Right - 500 + document.LeftMargin, document.Top + 15, 0);

                //页脚显示的位置  
                ColumnText.ShowTextAligned(cb, Element.ALIGN_CENTER, footer_left,
                       document.Right - 535 + document.LeftMargin, document.Bottom - 15, 0);

                ColumnText.ShowTextAligned(cb, Element.ALIGN_CENTER, footer_center,
                       document.Right - 300 + document.LeftMargin, document.Bottom - 15, 0);

                ColumnText.ShowTextAligned(cb, Element.ALIGN_CENTER, footer_right,
                       document.Right - 80 + document.LeftMargin, document.Bottom - 15, 0);
            }
        }

        /// <summary>
        /// 打开一个新页面时发生 
        /// </summary>
        public override void OnStartPage(PdfWriter writer, Document document)
        {
            if (PAGE_NUMBER)
            {
                writer.PageCount = writer.PageNumber - 1;
            }
        }

        /// <summary>
        /// 关闭PDF文档时发生该事件
        /// </summary>
        public override void OnCloseDocument(PdfWriter writer, Document document)
        {
            BaseFont bf = BaseFontAndSize(HeaderFooterFontName);
            tpl.BeginText();
            tpl.SetFontAndSize(bf, HeaderFooterFontSize);
            tpl.ShowText((writer.PageNumber - 1).ToString());//总页数 
            tpl.EndText();
            tpl.ClosePath();
        }
        #endregion

        #region 私有方法

        private static Font FontAndSize(string font_name, int size, int style, BaseColor baseColor)
        {
            BaseFont baseFont;
            //BaseFont.AddToResourceSearch("iTextAsian.dll");
            //BaseFont.AddToResourceSearch("iTextAsianCmaps.dll");
            Font font = null;
            string file_name = "";
            int fontStyle;
            switch (font_name)
            {
                case "黑体":
                    file_name = "SIMHEI.TTF";
                    break;
                case "华文中宋":
                    file_name = "STZHONGS.TTF";
                    break;
                case "宋体":
                    file_name = "SIMYOU.TTF";
                    break;
                case "宋体1":
                    file_name = "simsun.ttc";
                    break;
                default:
                    file_name = "SIMYOU.TTF";
                    break;
            }
            baseFont = BaseFont.CreateFont(@"c:/windows/fonts/" + file_name, BaseFont.IDENTITY_H, BaseFont.NOT_EMBEDDED);

            if (style < -1)
            {
                fontStyle = Font.NORMAL;
            }
            else
            {
                fontStyle = style;
            }
            font = new Font(baseFont, size, fontStyle, baseColor);
            return font;
        }
        private static BaseFont BaseFontAndSize(string font_name)
        {
            BaseFont baseFont;
            //BaseFont.AddToResourceSearch("iTextAsian.dll");
            //BaseFont.AddToResourceSearch("iTextAsianCmaps.dll");
            string file_name = "";
            switch (font_name)
            {
                case "黑体":
                    file_name = "SIMHEI.TTF";
                    break;
                case "华文中宋":
                    file_name = "STZHONGS.TTF";
                    break;
                case "宋体":
                    file_name = "SIMYOU.TTF";
                    break;
                default:
                    file_name = "SIMYOU.TTF";
                    break;
            }
            baseFont = BaseFont.CreateFont(@"c:/windows/fonts/" + file_name, BaseFont.IDENTITY_H, BaseFont.NOT_EMBEDDED);
            return baseFont;
        }

        #endregion

        #region 添加段落

        /// <summary>
        /// 添加段落
        /// </summary>
        /// <param name="content">内容</param>
        /// <param name="Alignment">对齐方式（1为居中，0为居左，2为居右）</param>
        /// <param name="SpacingAfter">段后空行数（0为默认值）</param>
        /// <param name="SpacingBefore">段前空行数（0为默认值）</param>
        /// <param name="MultipliedLeading">行间距（0为默认值）</param>
        public static Paragraph AddParagraph(string content, int Alignment, float SpacingAfter, float SpacingBefore, float MultipliedLeading)
        {
            Paragraph pra = new Paragraph(content, font);
            pra.Alignment = Alignment;
            pra.SpacingAfter = SpacingAfter;
            pra.SpacingBefore = SpacingBefore;
            pra.MultipliedLeading = MultipliedLeading;
            return pra;
        }

        public static Paragraph AddParagraph(string content, int Alignment, float MultipliedLeading)
        {
            Paragraph pra = new Paragraph(content, font);
            pra.Alignment = Alignment;
            pra.MultipliedLeading = MultipliedLeading;
            return pra;
        }

        public static void AddPhrase(PdfWriter writer, Document document, string content, float marginLift, float marginBottom)
        {
            Phrase phrase = new Phrase(content, font);

            PdfContentByte cb = writer.DirectContent;
            ColumnText.ShowTextAligned(cb, Element.ALIGN_CENTER, phrase,
                       marginLift + document.LeftMargin, marginBottom, 0);

        }

        #endregion

        #region 添加图片
        /// <summary>
        /// 添加图片
        /// <param name="Alignment">对齐方式 (0/1/2)</param>
        /// <param name="marginRight">页边距</param>
        /// <param name="marginBottom">页边距</param>
        /// </summary>
        public static iTextSharp.text.Image AddImage(string path, int Alignment, float marginRight, float marginBottom)
        {
            Image img = Image.GetInstance(new Uri(path));
            img.Alignment = Alignment;
            //等比缩放，宽与高的缩放系数哪个大就取哪一个（比如高的系数是0.8，宽的是0.7，则取0.7。这样图片就不会超出页面范围）
            if (img.Width > img.Height)
            {
                //这里计算图片的缩放系数，因为图片width>height,所以将图片旋转90度以适应页面，计算缩放系数的时候宽与高对调
                float PageHeight = PageSize.A4.Height - marginBottom * 3;
                double percentHeight = Math.Round((PageHeight / img.Width), 2);

                float PageWidth = PageSize.A4.Width - marginRight * 2;
                double percentWidth = Math.Round((PageWidth / img.Height), 2);

                double percent = percentHeight > percentWidth ? percentWidth : percentHeight;
                img.ScalePercent((float)percent * 100);
                img.RotationDegrees = 90f;
            }
            else
            {
                float PageHeight = PageSize.A4.Height - marginBottom * 3;
                double percentHeight = Math.Round((PageHeight / img.Height), 2);

                float PageWidth = PageSize.A4.Width - marginRight * 2;
                double percentWidth = Math.Round((PageWidth / img.Width), 2);

                double percent = percentHeight > percentWidth ? percentWidth : percentHeight;
                img.ScalePercent((float)percent * 100);
            }
            return img;
        }
        #endregion

    }
}

```

# 主函数调用方式
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
                this.lbShowProcess.Text = "正在准备统计数据...";
                this.pbProgress.Visible = true;
                statisticNum();
                this.pbProgress.Position = 30; 
                this.lbShowProcess.Text = "开始生成检查报告...";
                //文字水印1
                PdfUtils.GeneratePDF(resultDt, strPacName, isPassed, managerName, contactNum, unitName, outputFilePath, 1);
                //水印2
                // PdfUtils.GeneratePDF(resultDt, strPac,isPassed,outputFilePath, 2);
                //图片水印 
                // string picPath = string.Format("{0}/Resource/{1}", AppDomain.CurrentDomain.BaseDirectory,"logo.png");
                //PdfUtils.GeneratePDF(resultDt, strPac,isPassed,outputFilePath, 3,picPath);  
                this.lbShowProcess.Text = "检查报告导出完成";
            }
```

