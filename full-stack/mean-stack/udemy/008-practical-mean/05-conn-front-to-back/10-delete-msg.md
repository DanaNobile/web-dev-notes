# Deleting Messages
`messages.js`

```
router.delete('/:id', function(req, res, next) {
  Message.findById(req.params.id, function(err, message) {
    if (err) {
      return res.status(500).json({
        title: 'An error occurred',
        error: err
      });
    }
    if (!message) {
      return res.status(500).json({
        title: 'No Message Found',
        error: { message: 'Message not found' }
      });
    }
    message.remove(function(err, result) {
      if(err) {
        return res.status(500).json({
          title: 'An error occurred',
          error: err
        });
      }
      res.status(200).json({
        message: 'Message Deleted',
        obj: result
      });
    });
  });
})
```

`message.service.ts`

```
// more code
deleteMessage(message: Message) {
  this.messages.splice(this.messages.indexOf(message), 1);
  return this.http.delete(`http://localhost:3000/message/${message.messageId}`)
      .map((response: Response) => response.json())
      .catch((error: Response) => Observable.throw(error.json()));
}
// more code
```

`message.component.ts`

```
// more code
onDelete() {
  this.messageService.deleteMessage(this.message)
    .subscribe(
      result => console.log(result);
    );
}
// more code
```

* Restart server
* Refresh browser
* You should be able to delete messages

## Congrats
* You now know how to build an angular/node app to use CRUD
* You can communicate between NodeJS and the AngularJS frontend
* How to send data from frontend to backend and vice-versa

## Next - Authentication
* We'll create users
* Map users to messages
* Register users
* Authorized users are able to edit and delete messages
