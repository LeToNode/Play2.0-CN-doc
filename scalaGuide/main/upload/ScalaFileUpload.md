# Handling file upload

## Uploading files in a form using multipart/form-data

The standard way to upload files in a web application is to use a form with a special `multipart/form-data` encoding, which lets you mix standard form data with file attachment data.

Start by writing an HTML form:

```
@form(action = routes.Application.upload, 'enctype -> "multipart/form-data") {
    
    <input type="file" name="picture">
    
    <p>
        <input type="submit">
    </p>
    
}
```

Now define the `upload` action using a `multipartFormData` body parser:

```scala
def upload = Action(parse.multipartFormData) { request =>
  request.body.file("picture").map { picture =>
    import java.io.File
    val filename = picture.filename 
    val contentType = picture.contentType
    picture.ref.moveTo(new File("/tmp/picture"))
    Ok("File uploaded")
  }.getOrElse {
    Redirect(routes.Application.index).flashing(
      "error" -> "Missing file"
    )
  }
}
```

The `ref` attribute give you a reference to a `TemporaryFile`. This is the default way the `mutipartFormData` parser handles file upload.

> **Note:** As always, you can also use the `anyContent` body parser and retrieve it as `request.asMultipartFormData`.

## Direct file upload

Another way to send files to the server is to use Ajax to upload the file asynchronously in a form. In this case the request body will not have been encoded as `multipart/form-data`, but will just contain the plain file content.

In this case we can just use a body parser to store the request body content in a file. For this example, let’s use the `temporaryFile` body parser:

```scala
def upload = Action(parse.temporaryFile) { request =>
  request.body.moveTo(new File("/tmp/picture"))
  Ok("File uploaded")
}
```

## Writing your own body parser

If you want to handle the file upload directly without buffering it in a temporary file, you can just write your own `BodyParser`. In this case, you will receive chunks of data that you are free to push anywhere you want.

If you want to use `multipart/form-data` encoding, you can still use the default `mutipartFormData` parser by providing your own `PartHandler[FilePart[A]]`. You receive the part headers, and you have to provide an `Iteratee[Array[Byte], FilePart[A]]` that will produce the right `FilePart`.

> **Next:** [[Accessing an SQL database | ScalaDatabase]]