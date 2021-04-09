using System;
using System.Collections.Generic;
using System.Dynamic;
using System.Linq;
using System.Net.Http;
using System.Threading.Tasks;
using Amazon.Lambda.APIGatewayEvents;
using Amazon.Lambda.Core;
using Newtonsoft.Json;

// Assembly attribute to enable the Lambda function's JSON input to be converted into a .NET class.
[assembly: LambdaSerializer(typeof(Amazon.Lambda.Serialization.SystemTextJson.DefaultLambdaJsonSerializer))]

namespace assignment9BestSellers
{
    public class Function
    {

        HttpClient client = new HttpClient();


        public async Task<ExpandoObject> FunctionHandler(APIGatewayProxyRequest input, ILambdaContext context)
        {

            //-Retrieve JSON Query Param
            Dictionary<string, string> dict = (Dictionary<string, string>)input.QueryStringParameters;

            //get parameter value for list
            string Booklist = "";
            dict.TryGetValue("list", out Booklist); 

            //API KEY
            var apiKey = "api-key=uGcwOzSFyXvNME05AAMDSwAz5AfBVXUA";

            //complete url string
            var url = "https://api.nytimes.com/svc/books/v3/lists/current/"+Booklist+"?"+apiKey;

            //query nytimes
            string response = await client.GetStringAsync(url);

            //convert document to an oject
            dynamic expando = JsonConvert.DeserializeObject<ExpandoObject>(response);

            return expando;
        }
    }
}
