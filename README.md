using RestSharp;
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Net;
using System.Text;
using System.Web;
using System.Web.Services;

namespace MyWebStation.WS
{
    /// <summary>
    /// PacketTrian 的摘要说明
    /// </summary>
    [WebService(Namespace = "http://tempuri.org/")]
    [WebServiceBinding(ConformsTo = WsiProfiles.BasicProfile1_1)]
    [System.ComponentModel.ToolboxItem(false)]
    // 若要允许使用 ASP.NET AJAX 从脚本中调用此 Web 服务，请取消注释以下行。 
    // [System.Web.Script.Services.ScriptService]
    public class PacketTrian : System.Web.Services.WebService
    {

        [WebMethod(Description = "train_date当前日期,from_station出发地,to_station目的地，purpose_codes")]
        public void packTrianFc(
            DateTime train_datevalue,
            string from_station,
            string to_station,
            string purpose_codes
            )
        {
            Context.Response.Clear();
            Context.Response.ContentType = "application/json";
            string train_date = train_datevalue.ToString("yyyy-MM-dd");
            string url = "http://kyfw.12306.cn/otn/leftTicket/queryZ?leftTicketDTO.train_date=" + train_date + "&leftTicketDTO.from_station=" + from_station + "&leftTicketDTO.to_station=" + to_station + "&purpose_codes=" + purpose_codes + "";
            //string obj=HttpGet(url, "");

            string content = ""; //要发送的内容
            string contentType = "application/json; charset=utf-8"; //Content-Type
            var client = new RestClient(url);
            var request = new RestRequest(Method.GET);
            request.Timeout = 10000;
            //request.AddHeader("Cache-Control", "no-cache");          
            //request.AddParameter(contentType, content, ParameterType.RequestBody);

           IRestResponse response = client.Execute(request);
           Result rs= Result.parse(response.Content);
            //string ar = string.Empty;
            List<string> ar = new List<string>();

            
            StringBuilder sb = new StringBuilder();
            sb.Append("<div>");
            foreach (KeyValuePair<string, object> kv in rs.Data)
            {
                if (kv.Key == "result")
                // ar = kv.Value.ToString();
                {
                    //kv.Value.ToString().Replace("[", "{");
                    //kv.Value.ToString().Replace("]", "}");
                    //ar.Add(kv.Value.ToString());
                    string val2 = "";
                    //foreach (string val in kv.Value)
                    //{
                    //    val2 = val;

                    //}

                    int  arrr = kv.Value.ToString().Length;
                }
            }
           
            
            sb.Append("</div>");
            //Array[] li = rs.Data["result"];
            //return response.Content; //返回的结果
            Context.Response.Write(sb.ToString());
        }
        public class Result
        {
            private Dictionary<string,object> _data;

            public Dictionary<string, object> Data
            {
                get
                {
                    return _data;
                }

                set
                {
                    _data = value;
                }
            }

            public int Httpstatus
            {
                get
                {
                    return _httpstatus;
                }

                set
                {
                    _httpstatus = value;
                }
            }

            public string Messages
            {
                get
                {
                    return _messages;
                }

                set
                {
                    _messages = value;
                }
            }

            public bool Status
            {
                get
                {
                    return _status;
                }

                set
                {
                    _status = value;
                }
            }

            private int _httpstatus;


            private string _messages;

            private bool _status;


            public static Result parse(string json)
            {
                return Newtonsoft.Json.JsonConvert.DeserializeObject<Result>(json);
            }
        }
        
        private static string HttpPost(string url, string param)
        {

            string strURL = url;
            System.Net.HttpWebRequest request;
            request = (System.Net.HttpWebRequest)System.Net.WebRequest.Create(strURL);
            request.Method = "POST";
            request.ContentType = "application/json;charset=UTF-8";
            string paraUrlCoded = param;
            byte[] payload;
            payload = System.Text.Encoding.GetEncoding("GBK").GetBytes(paraUrlCoded);

            request.ContentLength = payload.Length;
            request.ContentType = "application/x-www-form-urlencoded";

            System.IO.Stream writer = request.GetRequestStream();
            writer.Write(payload, 0, payload.Length);

            writer.Close();
            System.Net.HttpWebResponse response;
            response = (System.Net.HttpWebResponse)request.GetResponse();
            System.IO.Stream s;
            s = response.GetResponseStream();
            string StrDate = "";
            string strValue = "";
            System.IO.StreamReader Reader = new System.IO.StreamReader(s, Encoding.GetEncoding("GBK"));

            while ((StrDate = Reader.ReadLine()) != null)
            {
                strValue += StrDate + "\r\n";
            }
            return strValue;
        }

        public string HttpGet(string Url, string postDataStr)
        {
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create(Url + (postDataStr == "" ? "" : "?") + postDataStr);
            request.Method = "GET";
            request.ContentType = "text/html;charset=UTF-8";

            HttpWebResponse response = (HttpWebResponse)request.GetResponse();
            Stream myResponseStream = response.GetResponseStream();
            StreamReader myStreamReader = new StreamReader(myResponseStream, Encoding.GetEncoding("utf-8"));
            string retString = myStreamReader.ReadToEnd();
            myStreamReader.Close();
            myResponseStream.Close();

            return retString;
        }
    }
}

