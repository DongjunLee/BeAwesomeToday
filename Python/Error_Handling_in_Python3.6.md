## Error Handling in Python 3.6

### Error case

1. Task ran without error. Data returned.
2. A known error occurred during task execution. No data returned.
3. A catastrophic runtime error occurred. No data returned

### Example

```python

"""
Caveat:

The following is not necessarily the 
most robust way of handling exceptions, IMO.
Python allows you to write custom exceptions 
that one can `raise from` others for good reason.
This is just meant as a way to think about how we 
would model the initial scenario described.
"""
from typing import NamedTuple, Optional
import requests
import logging
import json
import enum


class ApiInteraction(enum.Enum):
    """The 3 possible states we can expect when interacting with the API."""
    SUCCESS = 1
    ERROR = 2
    FAILURE = 3


class ApiResponse(NamedTuple):
    """
    This is sort of a really dumbed-down version of an HTTP response,
    if you think of it in terms of status codes and response bodies.
    """
    status: ApiInteraction
    payload: Optional[dict]


        
def hit_endpoint(url: str) -> ApiResponse:
    """
    1. Send an http request to a url
    2. Parse the json response as a dictionary
    3. Return an ApiResponse object
    """
    
    
    try:
        
        response = requests.get(url) # step 1
        payload = response.json() # step 2
        
    except json.decoder.JSONDecodeError as e:
        
        # something went wrong in step 2; we knew this might happen
        
        # log a simple error message
        
        logging.error(f'could not decode json from {url}')
        
        # log the full traceback at a lower level
        
        logging.info(e, exc_info=True)
        
        # since we anticipated this error, make the
        # ApiResponse.status an ERROR as opposed to a failure
        
        return ApiResponse(ApiInteraction.ERROR, None)

    # 'except Exception' is seen as an anti-pattern by many but
    # this is just a trivial example. Another article for another time.
    except Exception as e:
        
        # something went wrong in step 1 or 2 that
        # we couldn't anticipate
        
        # log the exception with the traceback
        
        logging.error(f"Something bad happened trying to reach {url}")
        logging.info(e, exc_info=True)
        
        # Since something catastrophic happened that
        # we didn't anticipate i.e. (DivideByBananaError)
        # we set the ApiResponse.status to FAILURE
        
        return ApiResponse(ApiInteraction.FAILURE, None)
    
    else:
        
        # Everything worked as planned! No errors!
        
        return ApiResponse(ApiInteraction.SUCCESS, payload)


# Python is awesome. We can either use the function by itself
# or use it as a constructor for our ApiResponse class 
# by doing thefollowing:


ApiResponse.from_url = hit_endpoint

```

```python

def test_endpoint_response():

    url = 'http://httpbin.org/headers'
    response = ApiResponse.from_url(url)
    assert response.status == ApiInteraction.SUCCESS
    assert response.status == hit_endpoint(url).status # our function and constructor work the same!
    assert response.payload is not None


    url = 'http://twitter.com'
    response = ApiResponse.from_url(url)
    assert response.status == ApiInteraction.ERROR
    assert response.status == hit_endpoint(url).status
    assert response.payload is None


    url = 'foo'
    response = ApiResponse.from_url(url)
    assert response.status == ApiInteraction.FAILURE
    assert response.status == hit_endpoint(url).status
    assert response.payload is None
        
    
test_endpoint_response()

```

```bash
ERROR:root:could not decode json from http://twitter.com
ERROR:root:could not decode json from http://twitter.com
ERROR:root:Something bad happened trying to reach foo
ERROR:root:Something bad happened trying to reach foo
```


## Reference

- [Postmodern Error Handling in Python 3.6](http://journalpanic.com/post/postmodern-error-handling/)
- [Data Structures in Python 3](./Data_structure.md)



