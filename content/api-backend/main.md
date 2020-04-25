---
weight: 10
title: API Reference
---

# Introduction

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
