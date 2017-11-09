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
