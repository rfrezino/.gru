---
type: "Module"
title: "W3C parser"
description: "Splits raw log lines into W3cLogLine fields."
---
`parse_w3c_loglines_to_loglines` returns `W3cLogParserResult(success_linelogs, error_linelogs)`; any exception on a line puts it in the error list.
Fields: remote_host, remote_log_name, authenticated_user_name, request_datetime, request_body, status code, bytes (all strings).
The mapper then produces typed `LogLine` (datetime, `RequestBody` with verb, section, url, http version; ints).
