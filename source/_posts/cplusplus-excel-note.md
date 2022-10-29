---
title: C#读取，生成Excel
date: 2019-04-10 10:58:28
tags: 
    - C#
categories: C#
---
使用NPOI开源插件读取和操作Excel等
<!--more-->
# ExcelUtils
```
using GDZLCHECK.Forms;
using NPOI.HSSF.UserModel;
using NPOI.SS.UserModel;
using NPOI.SS.Util;
using NPOI.XSSF.UserModel;
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
    class ExcelUtils
    {
        public static IWorkbook GetWorkbook(string filePath)
        {
            IWorkbook workbook;
            string fileType = System.IO.Path.GetExtension(filePath);
            if (string.IsNullOrEmpty(fileType))
                return null;
            try
            {
                using (FileStream file = new FileStream(filePath, FileMode.Open, FileAccess.Read))
                {
                    if (fileType == ".xls")
                    {
                        workbook = new HSSFWorkbook(file) as IWorkbook;
                    }
                    else if (fileType == ".xlsx")
                    {
                        workbook = new XSSFWorkbook(file);
                    }
                    else
                    {
                        return null;
                    }
                    file.Close();
                    return workbook;
                }
            }
            catch (Exception ex)
            {
                MessageBox.Show("打开excel失败，文件可能正被打开");
                Console.WriteLine( ex.Message.ToString());
               
               // MainForm.logger.Error("Error: 打开"+filePath+" excel失败，文件可能正被打开");
                return null;
            }
        }
        /// <summary>
        /// 判断指定行列所在的单元格是否为合并单元格，
        /// </summary>
        /// <param name="sheet">Excel工作表</param>
        /// <param name="rowIndex">行索引，从0开始</param>
        /// <param name="columnIndex">列索引，从0开始</param>
        /// <returns>返回是否为合并单元格的布尔(Boolean)值</returns>
        public static bool IsMergeCell(ISheet sheet, int rowIndex, int columnIndex)
        {


            for (int i = 0; i < sheet.NumMergedRegions; i++)
            {
                CellRangeAddress range = sheet.GetMergedRegion(i);
                sheet.IsMergedRegion(range);

                //这种算法只有当指定行列索引刚好是合并单元格的第一个跨度行第一个跨度列时才能取得合并单元格的跨度
                //if (range.FirstRow == rowIndex && range.FirstColumn == columnIndex)
                //{
                //    dimension.DataCell = sheet.GetRow(range.FirstRow).GetCell(range.FirstColumn);
                //    dimension.RowSpan = range.LastRow - range.FirstRow + 1;
                //    dimension.ColumnSpan = range.LastColumn - range.FirstColumn + 1;
                //    dimension.FirstRowIndex = range.FirstRow;
                //    dimension.LastRowIndex = range.LastRow;
                //    dimension.FirstColumnIndex = range.FirstColumn;
                //    dimension.LastColumnIndex = range.LastColumn;
                //    break;
                //}

                if ((rowIndex >= range.FirstRow && range.LastRow >= rowIndex) && (columnIndex >= range.FirstColumn && range.LastColumn >= columnIndex))
                {

                    break;
                }
            }

            bool result;
            if (rowIndex >= 0 && sheet.LastRowNum > rowIndex)
            {
                IRow row = sheet.GetRow(rowIndex);
                if (columnIndex >= 0 && row.LastCellNum > columnIndex)
                {
                    ICell cell = row.GetCell(columnIndex);
                    result = cell.IsMergedCell;
                }
                else
                {
                    result = false;
                }
            }
            else
            {
                result = false;
            }

            return result;
        }
        public static object GetCellValue(ICell cell)
        {
            object value = "";
            try
            {
                if (cell.CellType != CellType.Blank)
                {
                    switch (cell.CellType)
                    {
                        case CellType.Numeric:
                            // Date comes here  
                            if (DateUtil.IsCellDateFormatted(cell))
                            {
                                value = cell.DateCellValue;
                            }
                            else
                            {
                                // Numeric type  
                                value = cell.NumericCellValue;
                            }
                            break;
                        case CellType.Boolean:
                            // Boolean type  
                            value = cell.BooleanCellValue;
                            break;
                        case CellType.Formula:
                            value = cell.CellFormula;
                            break;
                        default:
                            // String type  
                            value = cell.StringCellValue;
                            break;
                    }
                }
            }
            catch (Exception)
            {
                value = "";
            }

            return value;
        }

        //判断是否为空
        public static bool isEmptyRow(ICell cell)
        {
            if (cell == null)
                return true;
            else
            {
                if (cell.CellType == CellType.Blank || string.IsNullOrEmpty(cell.ToString().Trim()))
                    return true;
                else
                    return false;
            }
        }

        public static DataTable ExportToDataTable(ISheet sheet)
        {
            DataTable dt = new DataTable();

            //默认，第一行是字段
            IRow headRow = sheet.GetRow(0);

            //设置datatable字段
            for (int i = headRow.FirstCellNum, len = headRow.LastCellNum; i < len; i++)
            {
                dt.Columns.Add(headRow.Cells[i].StringCellValue);
            }
            //遍历数据行
            for (int i = (sheet.FirstRowNum + 1), len = sheet.LastRowNum + 1; i < len; i++)
            {
                IRow tempRow = sheet.GetRow(i);
                DataRow dataRow = dt.NewRow();

                //遍历一行的每一个单元格
                for (int r = 0, j = tempRow.FirstCellNum, len2 = tempRow.LastCellNum; j < len2; j++, r++)
                {

                    ICell cell = tempRow.GetCell(j);

                    if (cell != null)
                    {
                        switch (cell.CellType)
                        {
                            case CellType.String:
                                dataRow[r] = cell.StringCellValue;
                                break;
                            case CellType.Numeric:
                                dataRow[r] = cell.NumericCellValue;
                                break;
                            case CellType.Boolean:
                                dataRow[r] = cell.BooleanCellValue;
                                break;
                            default: dataRow[r] = "";
                                break;
                        }
                    }
                }
                dt.Rows.Add(dataRow);
            }
            return dt;
        }
    }
}

```
