# Description

This exposes IBM's HttpFS REST API for interacting with HDFS (Hadoop Distributed File System). It exposes a simple request function upon which additional routes can be created.

I've only written functions for listing directories and uploading files. However, full documentation on IBM's HttpFS [can be found here](http://www-01.ibm.com/support/knowledgecenter/SSPT3X_3.0.0/com.ibm.swg.im.infosphere.biginsights.admin.doc/doc/admin_fileupload_rest_apis.html) if you want to implement other routes.

# Install

    npm install ibm-hdfs

# Use

```
var HDFS = require('ibm-hdfs');

var hdfs = new HDFS({
    user: 'foo',
    password: 'bar',
    url: 'https://server:port/webhdfs/v1/'
});
```

# Routes

## List a directory

The first argument is the remote directory on HDFS. This will list all the files in that remote directory.

Returns a promise.

```
hdfs.listDirectory('dir').then(function(response) {
    console.log(response);
});
```

## Upload a file

First argument is the remote path to upload to.

Second argument is either a string consisting of data to upload, or a string containing a local file path. The function will first attempt to find the file locally, and on fail use the string contents.

Returns a promise.

```
// using a string as data
var contents = 'foo bar!';
hdfs.upload('/path/to/remote/file', contents).then(function(response) {
    console.log(response);
});

// using a local file
hdfs.upload('/path/to/remote/file', '/path/to/local/file.txt).then(function(response) {
    console.log(response);
});
```

## Download a file

First argument is the remote path to download.

Second argument is optional and specifies a local file path to save the file to.

Returns a promise, with the first argument of the fulfillment function being the contents.

```
// retrieving contents directly
hdfs.download('/path/to/remote/file').then(function(response) {
    console.log(response); // file contents
});

// specifying local file to download to
hdfs.download('/path/to/remote/file', '/path/to/local/file').then(function(response) {
    console.log(response); // file contents
});
```

## Remove a file

First argument is the file to remove. Can be a directory or file.

Note: recursive is currently *not* supported, you'll have to implement this yourself.

Returns a promise.

```
hdfs.remove('/path/to/remote/file').then(function() {
    console.log('yay');
});

## Errors

All functions will return a promise that rejects with an error. That error can take two forms. If there is a permission error, the error will look like:

```
{
    message: 'Permission denied: user=biblumix, access=WRITE, inode="/":hdfs:biadmin:drwxr-xr-x',
    javaClassName: 'org.apache.hadoop.security.AccessControlException',
    exception: 'AccessControlException',
    args: {
        user: 'biblumix',
        access: 'WRITE',
        inode: '"/":hdfs:biadmin:drwxr-xr-x'
    }
}
```

Otherwise, the error will simply include the error exception like:

```
{
    exception: 'foo'
}
