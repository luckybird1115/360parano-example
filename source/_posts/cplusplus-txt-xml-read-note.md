---
title: C#读取txt和xml文件等
date: 2019-04-10 11:05:59
tags: C#
categories: C#
---
读取txt和xml等，生成Dictionary等
<!--more-->
# TXT文件读取
pac文件格式：
11000,XX省   
11100,XX市    
11101,XXXX区      
11102,XXXX1区    
  
```
public static Dictionary<string, string> countyPacMap = new Dictionary<string, string>();//存储区域PAC码对应信息
 FileStream file = new FileStream("Config/PAC.txt", FileMode.OpenOrCreate);
            StreamReader reader = new StreamReader(file, UnicodeEncoding.GetEncoding("UTF-8"));
            string strLine = string.Empty;
            while ((strLine = reader.ReadLine()) != null)
            {
                strLine = strLine.Trim().ToString();
                string[] nameAndCode = strLine.Split(',');
                string name = nameAndCode[1];
                string pac = nameAndCode[0];
                countyPacMap.Add(name, pac);
            }
            file.Close();
```
# XML读取
XML格式：  
```
<Directory Name="XX" >
  <Directory Name="1-采样数据" >
    <File DestinationDirectory="1-采样数据" isCheckAttribute="true" type="1" sheetNum="1" >XXX.txt</File>
    <File DestinationDirectory="1-采样数据">2-登记表.pdf</File>
    <File DestinationDirectory="1-采样数据">3-送样单.pdf</File>
  </Directory>
</Directory>   
```
读取XML  
```
 public static List<FieldModel> getFieldsFromXml(String xmlName, int index)
         {
             string m_strConfig = "Tables/";
             List<FieldModel> result = new List<FieldModel>();
             XmlDocument doc = new XmlDocument();
             doc.Load(m_strConfig + xmlName + ".xml");             
             XmlNode root = doc.SelectSingleNode("Table");
             XmlNode sheet = root.ChildNodes[index];
             // 得到根节点的所有子节点
             XmlNodeList xnl = sheet.ChildNodes;
             foreach (XmlNode xn in xnl)
             {
                 FieldModel fieldModel = new FieldModel();
                 // 将节点转换为元素，便于得到节点的属性值
                 XmlElement xe = (XmlElement)xn;
                 fieldModel.FieldName = xe.GetAttribute("Name").ToString();
                 fieldModel.FieldType = xe.GetAttribute("Type").ToString();
                 fieldModel.RuleType = xe.GetAttribute("RuleType").ToString();
                 fieldModel.RuleParms = xe.GetAttribute("RuleParms").ToString();
                 result.Add(fieldModel);
             }
             return result;
         }
         public static string[] getFieldCNames(ArrayList array)
         {
             int nums = array.Count;
             string[] names = new string[nums];
             for (int i = 0; i < nums; i++)
             {
                 FieldModel fieldModel = (FieldModel)array[i];
                 names[i] = fieldModel.FieldName;
             }
             return names;
         }
//FieldModel类
class FieldModel
    {
        public FieldModel()
        {

        }
        private string fieldName;

        public string FieldName
        {
            get { return fieldName; }
            set { fieldName = value; }
        }
       
        private string ruleType;

        public string RuleType
        {
            get { return ruleType; }
            set { ruleType = value; }
        }
        private string fieldType;

        public string FieldType
        {
            get { return fieldType; }
            set { fieldType = value; }
        }
        private string ruleParms;

        public string RuleParms
        {
            get { return ruleParms; }
            set { ruleParms = value; }
        }




    }
```

