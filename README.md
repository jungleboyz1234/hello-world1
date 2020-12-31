# hello-world1
 ReverseProxy is an HTTP Handler that takes an incoming request and sends it to another server, proxying the response back to the client.

ReverseProxy by default sets the client IP as the value of the X-Forwarded-For header.

If an X-Forwarded-For header already exists, the client IP is appended to the existing values. As a special case, if the header exists in the Request.Header map but has a nil value (such as when set by the Director func), the X-Forwarded-For header is not modified.

To prevent IP spoofing, be sure to delete any pre-existing X-Forwarded-For header coming from the client or an untrusted proxy.

type ReverseProxy struct {
    // Director must be a function which modifies
    // the request into a new request to be sent
    // using Transport. Its response is then copied
    // back to the original client unmodified.
    // Director must not access the provided Request
    // after returning.
    Director func(*http.Request)

    // The transport used to perform proxy requests.
    // If nil, http.DefaultTransport is used.
    Transport http.RoundTripper

    // FlushInterval specifies the flush interval
    // to flush to the client while copying the
    // response body.
    // If zero, no periodic flushing is done.
    // A negative value means to flush immediately
    // after each write to the client.
    // The FlushInterval is ignored when ReverseProxy
    // recognizes a response as a streaming response;
    // for such responses, writes are flushed to the client
    // immediately.
    FlushInterval time.Duration

    // ErrorLog specifies an optional logger for errors
    // that occur when attempting to proxy the request.
    // If nil, logging is done via the log package's standard logger.
    ErrorLog *log.Logger // Go 1.4

    // BufferPool optionally specifies a buffer pool to
    // get byte slices for use by io.CopyBuffer when
    // copying HTTP response bodies.
    BufferPool BufferPool // Go 1.6

    // ModifyResponse is an optional function that modifies the
    // Response from the backend. It is called if the backend
    // returns a response at all, with any HTTP status code.
    // If the backend is unreachable, the optional ErrorHandler is
    // called without any call to ModifyResponse.
    //
    // If ModifyResponse returns an error, ErrorHandler is called
    // with its error value. If ErrorHandler is nil, its default
    // implementation is used.
    ModifyResponse func(*http.Response) error // Go 1.8

    // ErrorHandler is an optional function that handles errors
    // reaching the backend or errors from ModifyResponse.
    //
    // If nil, the default is to log the provided error and return
    // a 502 Status Bad Gateway response.
    ErrorHandler func(http.ResponseWriter, *http.Request, error) // Go 1.11
