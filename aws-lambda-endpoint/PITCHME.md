---
### Building an AWS Lambda RPC handler
<span>That is I want to create an AWS Lambda function that will be available by HTTPs from outside</span>
---
```python
def my_hander(event, context):
    print(event)
    return {
        'event': event
    }
```
---
create lambda function
---
set up aws api gateway
---
#### Some Related Links

- [Mapping Request Body](https://kennbrodhagen.net/2015/12/06/how-to-create-a-request-object-for-your-lambda-event-from-api-gateway/) (via [SO question](https://stackoverflow.com/questions/31329958/how-to-pass-a-querystring-or-route-parameter-to-aws-lambda-from-amazon-api-gatew))
