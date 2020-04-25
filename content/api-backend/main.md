---
weight: 10
title: API Reference
---

# Introduction

```java
public class ItoApi {

	private static final String BASE_URL = "https://tcn.ito-app.org/tcnreport";

	public static List<byte[]> refreshInfectedUUIDs() {
		List<byte[]> reports = new LinkedList<>();
		HttpURLConnection urlConnection = null;
		try {
			//TODO use a more sophisticated library
			URL url = new URL(BASE_URL);
			urlConnection = (HttpURLConnection) url.openConnection();
			urlConnection.addRequestProperty("Accept", "application/octet-stream");
			InputStream in = urlConnection.getInputStream();
			byte[] base = new byte[BASELENGTH];
			byte[] memo;
			int readBytes;
			while ((readBytes = in.read(base, 0, BASELENGTH)) == BASELENGTH) {
				int memolength = (int) base[BASELENGTH - 1] & 0xFF;
				memo = new byte[memolength];
				if (in.read(memo, 0, memolength) < memolength) {
					throw new RuntimeException("Parsing from Server failed");
				}
				System.out.println("Downloaded TCN Report: " + encodeHexString(base) + encodeHexString(memo));
				// use PushbackInputstream and get rid of BB?
				ByteBuffer report = ByteBuffer.allocate(BASELENGTH + memolength);
				report.put(base);
				report.put(memo);
				reports.add(report.array());
			}
			if (readBytes > 0)
				throw new RuntimeException("Parsing from Server failed");
		} catch (MalformedURLException e) {
			Log.wtf(LOG_TAG, "Malformed URL?!", e);
			throw new RuntimeException(e);
		} catch (IOException e) {
			e.printStackTrace();
		} finally {
			if (urlConnection != null) {
				urlConnection.disconnect();
			}
		}
		return reports;
	}
}
```

```go
package main

import (
	"bytes"
	"net/http"
	"github.com/ito-org/go-backend/tcn"
)

func main() {
	_, rak, report, err := tcn.GenerateReport(0, 1, []byte("symptom data"))
	if err != nil {
		t.Error(err)
		return
	}

	signedReport, err := tcn.GenerateSignedReport(rak, report)
	if err != nil {
		t.Error(err)
		return
	}

	b, err := signedReport.Bytes()
	if err != nil {
		t.Error(err)
		return
	}
	rec, req := http.NewRequest("POST", "/tcnreport", bytes.NewReader(b))
	req, _ := http.NewRequest("GET", "/tcnreport", nil)
}
```

The ito backend has intentionally been kept very lean.

There are only two endpoints:

- `GET https://tcn.ito-app.org/tcnreport`
- `POST https://tcn.ito-app.org/tcnreport`
