# ACTA

## Abstracts

**Actor Action Object Meta** (ACTA) provides a humane way of representing Activities within a socially themed application. ACTA aims to deliver ease of understanding while maintaining clarity over REST implementations. It is a request protocol built for social networks. While it is possible to declare responses with ACTA, it is the engineer's free will.

## Copyright Notice

Copyright (c) 2014 Batista Harahap

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

## Introduction

ACTA is created out of a necessity to further streamline HTTP requests fitting modern social network applications. While REST is an excellent concept, much of the concept is dated back to 1995. ACTA is built with simple rules of thumb below (heavily inspired by Python):

- Less is More
- Complex is preferred than complicated

With the rules in mind, ACTA is designed to be implemented and understood quickly. An example of how simple ACTA is is represented below.

### Sending Message

Sending a message to another user is something that is common for any social network. Let's see how we can implement this with both REST and ACTA.

#### REST

`https://api.icehouse.com/messages/@john/team/chief/`

```
+ Request
	{
		'message': 'Hello Chief!'
	}
```

```python
import requests

requests.post('https://api.icehouse.com/messages/@john/team/chief/', data={'message': 'Hello Chief!'})
```

The URL above is an endpoint for John to send messages to his teammates. In this particular case, John wants to send a message to the user Chief. The HTTP method for this endpoint is of course POST.

#### ACTA

`https://api.icehouse.com/acta/`

```
+ Request
	{
		'actor': {
			'type': 'user'
			'id': '@me'						// > John
		},
		'action': 523						// Verbs.SEND - An enumerable predefined
		'object': {
			'type': 'message'
			'data': {
				'body': 'Hello Client!'
			}
		},
		'meta': {
			'data': {
				'to': 'chief'
			}
		}
	}
```

```python
from acta import Acta, Verbs
import requests

req_body = Acta
			.actor(type='user', 
				   id='@me')
			.action(Verbs.SEND)
			.object(type='message',
					data=dict(body='Hello Client!'))
			.meta(data={
				'to': 'chief'
			})

requests.post('https://api.icehouse.com/acta', data=req_body)
```

In a glance it looks complex. The number of bytes being occupied is significantly more than the REST example. But, try typing in the Python codes.

### Full Text Search

Search is always powerful. And full of parameters. ACTA loves complexity.

#### REST

`https://api.icehouse.com/search/?q=blue%20jeans&category_id=12&since_id=1400088497&excluded_item_ids=12,14,18&price_min=0&price_max=100&sort=2&sort_direction=ASC`

```python
import requests

requests.get('https://api.icehouse.com/search/?q=blue%20jeans&category_id=12&since_id=1400088497&excluded_item_ids=12,14,18&price_min=0&price_max=100&sort=2&sort_direction=ASC')
```