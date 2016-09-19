# API Gateway request 설정

## Mapping Template

The template is made using the [Velocity Template Language](https://velocity.apache.org/engine/1.7/user-guide.html) created by Apache

## Integration Request

이 부분을 수정해야 함.

 application/json Content-Type 을 추가.
 
## Mapping Template

```python
{
  "body" : $input.json('$'),
  "headers": {
    #foreach($header in $input.params().header.keySet())
    "$header": "$util.escapeJavaScript($input.params().header.get($header))" #if($foreach.hasNext),#end

    #end
  },
  "method": "$context.httpMethod",
  "resourcePath": "$context.resourcePath",
  "params": {
    #foreach($param in $input.params().path.keySet())
    "$param": "$util.escapeJavaScript($input.params().path.get($param))" #if($foreach.hasNext),#end

    #end
  },
  "query": {
    #foreach($queryParam in $input.params().querystring.keySet())
    "$queryParam": "$util.escapeJavaScript($input.params().querystring.get($queryParam))" #if($foreach.hasNext),#end

    #end
  }  
}
```

- **body**: contains the parsed JSON body of the request for PATCH/POST/PUT method requests.
- **headers**: contains all the HTTP headers that appeared in the API Gateway request.
- **method**: contains the HTTP method used to call the API. This property makes it easier to provide the same Lambda to several different API Gateway methods.
- **resourcePath**: resource path ex) /api/~~.
- **params**: contains the Request Path Parameters that you register with API Gateway by using curly-braces in the Method name.
- **query**: contains the URL Query String Parameters that you register with API Gateway in the Method Request portion of the API.

## Resources

- [Velocity Template Language Reference](https://velocity.apache.org/engine/1.7/user-guide.html)
- [AWS Mapping Template Reference](http://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-mapping-template-reference.html)