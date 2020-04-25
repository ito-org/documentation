---
weight: 10
title: API Reference
---

# API

The ito API has intentionally been kept lean, so there is just one endpoint: [`https://tcn.ito-app.org/tcnreport`](https://tcn.ito-app.org/tcnreport)

This way our code stays easy to understand and to audit.

> Initialization of a basic client

```go
package main

import (
	"bytes"
	"net/http"
	"github.com/ito-org/go-backend/tcn"
)

const baseUrl = "https://tcn.ito-app.org"
```

```java
public class ItoApi {
	private static final String BASE_URL
		= "https://tcn.ito-app.org";
}
```

Here's how to setup a basic ito client.

## Fetching reports

With a `GET` request you can download all recent reports of infected users. Those are returned as a bytestream (MIME type `application/octet-stream`) of TCN reports as per the protocol definition.

> Example client code for fetching recent reports

```java
private static final int BASELENGTH = 70;
public static List<byte[]> getReports() {
	List<byte[]> reports = new LinkedList<>();
	HttpURLConnection urlConnection = null;
	try {
		URL url = new URL(BASE_URL + "/tcnreport");
		urlConnection
			= (HttpURLConnection) url.openConnection();
		urlConnection.addRequestProperty(
			"Accept",
			"application/octet-stream"
		);
		InputStream in = urlConnection.getInputStream();
		byte[] base = new byte[BASELENGTH];
		byte[] memo;
		int readBytes;
		while ((readBytes = in.read(base, 0, BASELENGTH))
				== BASELENGTH) {
			int memolength
				= (int) base[BASELENGTH - 1] & 0xFF;
			memo = new byte[memolength];
			if (in.read(memo, 0, memolength)
					< memolength) {
				throw new RuntimeException(
					"Parsing from server failed"
				);
			}
			System.out.println(
				"Downloaded TCN Report: "
				+ encodeHexString(base)
				+ encodeHexString(memo)
			);
			ByteBuffer report = ByteBuffer.allocate(
				BASELENGTH + memolength
			);
			report.put(base);
			report.put(memo);
			reports.add(report.array());
		}
		if (readBytes > 0)
			throw new RuntimeException(
				"Parsing from server failed"
			);
	} catch (MalformedURLException e) {
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
```

```go
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

## Publishing a report

With a `POST` request you can report yourself as infected.

> Example client code for submitting a report

```java
public static void publishReport(
	byte[] report
) throws IOException {
	HttpURLConnection urlConnection = null;
	try {
		URL url = new URL(BASE_URL + "/tcnreport");
		urlConnection
			= (HttpURLConnection) url.openConnection();
		urlConnection.setDoOutput(true);
		urlConnection.addRequestProperty(
			"Content-Type",
			"application/octet-stream"
		);
		OutputStream outputStream
			= new BufferedOutputStream(
				urlConnection.getOutputStream()
			  );
		outputStream.write(report);
		outputStream.close();

		InputStream inputStream
			= urlConnection.getInputStream();
		inputStream.read();
		inputStream.close();
	} catch (MalformedURLException e) {
		throw new RuntimeException(e);
	} finally {
		if (urlConnection != null)
			urlConnection.disconnect();
	}
}
```