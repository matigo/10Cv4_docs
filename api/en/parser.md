# `/parser` - Markdown Formatting Parser

### Summary

There are many different flavours of Markdown in use across the Internet, and 10Centuries has tried to follow the Standard Markdown format that was first released by [John Gruber](https://daringfireball.net/projects/markdown/) in December 2004. That said, there are *some* custom formatting options in 10Centuries which may result in some unexpected text rendering. To this end, the `/parser` API format was created.

> **Note:** All API requests to the text parser must have a valid authorization token in the HTTP header.

### Sections

1. Parsing a Text Object

### Parsing a Text Object

10Centuries allows anybody with a valid authentication token to render a text object into HTML. If the text string passed contains valid Markdown formatting, it will be converted accordingly. All unicode characters are supported, including emoji and other less-common symbols.

From your application, send a request to the following endpoint:

```
POST https://api.10centuries.org/parser
```

##### Required Variables:

```
content: {post content}
```

##### Optional Variables:

```
title: {Title for Blog Post, Draft, Page, or Podcast}
```

This information can be sent URL-encoded in the POST body or as a JSON package.

##### Example

```
curl -X POST -H "Authorization: {Authorization Token}" \
-H "Content-Type: application/x-www-form-urlencoded" \
-d "content=This is, perhaps, one of the most boring posts in the history of the universe." \
"https://api.10centuries.org/parser"
```

If the post was successful, the API will respond with a JSON package:

```
{
    "meta": {
        "code": 200
    },
    "data": {
        "title": "",
        "content": {
            "text": "This is, perhaps, one of the most boring posts in the history of the universe.",
            "html": "<p>This is, perhaps, one of the most boring posts in the history of the universe.</p>"
        }
    }
}
```

If posting failed or there was some other problem, the API will respond with an error:

```
{
    "meta": {
        "code": 404,
        "text": "No Text Received"
    },
    "data": false
}
```