# HTTP 进度事件

将`reportProgress`选项设置为true，可以对进度事件进行跟踪。

⚠️每个进度事件都会触发更改检测。

### 进度事件

```typescript
longRequest() {
    const request = new HttpRequest(
        "POST", "/api/test-request", {}, 
         {reportProgress: true});

    this.http.request(request)
        .subscribe(
            event => {
                if (event.type === HttpEventType.DownloadProgress) {
                    console.log("Download progress event", event);
                }
                if (event.type === HttpEventType.UploadProgress) {
                    console.log("Upload progress event", event);
                }
                if (event.type === HttpEventType.Response) {
                    console.log("response received...", event.body);
                }
            }
        );
}
```