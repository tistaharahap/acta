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

`https://api.icehouse.com/messages/@john/team/chief/` - `POST`

```
+ Request
	{
		'message': 'Hello Chief!'
	}
```

```
import requests

requests.post('https://api.icehouse.com/messages/@john/team/chief/', data={'message': 'Hello Chief!'})
```

The URL above is an endpoint for John to send messages to his teammates. In this particular case, John wants to send a message to the user Chief. The HTTP method for this endpoint is of course POST.

#### ACTA

`https://api.icehouse.com/acta/` - `POST`

```
+ Request
	{
		'actor': {
			'type': 'user'
			'id': '@me'
		},
		'action': 523
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

```
from acta import Acta, Verbs, Objects
import requests

req_body = Acta
			.actor(type=Objects.USER, 
				   id='@me')
			.action(Verbs.SEND)
			.object(type=Objects.MESSAGE,
					data=dict(body='Hello Client!'))
			.meta(data=dict(to='chief'))

requests.post('https://api.icehouse.com/acta', data=req_body)
```

In a glance it looks complex. The number of bytes being occupied is significantly more than the REST example. But, try typing in the Python codes.

### Retrieving a User Profile

Viewing a User Profile is basic to any social network. Let's see how we can do this with ACTA.

#### REST

`https://api.icehouse.com/user/john` - `GET`

```
import requests

requests.get('https://api.icehouse.com/user/john')
```

#### ACTA

`https://api.icehouse.com/acta` - `POST`

```
from acta import Acta, Verbs, Objects
import requests

req_body = Acta
			.actor(type=Objects.USER,
				   id='@me')
			.action(Verbs.VIEW)
			.object(type=Objects.USER_PROFILE,
					data=dict(to='john'))
requests.post('https://api.icehouse.com/acta', data=req_body)
```

## Specification

The specifics of ACTA are what governs how implementations should behave. When implementing ACTA, the only source of absolute truth is this document. Do not make any assumptions. There is only **one** true way for ACTA. Should any assumptions arises, raise as an Issue on Github.

Every ACTA request is routed through the use of JSON objects as the request body. This JSON **must** be formed by using Acta client libraries. Do not generate the JSON request body by hand.

### Terminology

Terminologies in ACTA are simple enough to only consists of **4** terms. The terms are detailed in the following sub-sections.
	
#### Actor

An actor is the subject of any activity. The type of an Actor must be a defined constant already defined with any Acta library. If it is not defined, implementers extend the library to suit to their needs. In no way should an implementation directly reference a value.

```
Code:
	Acta.actor(type=Objects.USER,
			   id='@me')
	
	Acta.actor(type=Objects.USER,
			   id='@anon')

Resulting JSON:
	{
		'actor': {
			'type': 23			// Objects.USER
			'id': '@me'
		}
	}
	
	{
		'actor': {
			'type': 23			// Objects.USER
			'id': '@anon'
		}
	}
```

##### Reserved Actor IDs

All reserved IDs are prefixed with `@`. Reserved Actor IDs are described below.

```
@anon
	This ID does not point to any object.

@me
	This ID points to the requesting user object's identity.

@friend
	This ID still does not point to any object. But, from this ID we can 
	know that the requesting user is a friend of @me.
```

#### Action