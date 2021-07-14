# filesystem

A custom filesystem that only returns files.  
This filesystem is meant to be used as an argument to `http.FileServer`.

## Usage

```go
import (
	"net/http"
	"os"
	"path/filepath"

	"github.com/henrikac/filesystem"
)

func main() {
	pwd, _ := os.Getwd()
	assets := filepath.Join(pwd, "assets")
	fileserver := http.FileServer(filesystem.FileSystem{http.Dir(assets)})

	http.HandleFunc("/", func(w http.ResponseWriter, req *http.Request) {
		io.WriteString(w, "Hello, world!\n")
	}
	http.Handle("/static/", http.StripPrefix("/static/", fileserver))
	log.Fatal(http.ListenAndServe(":1337", nil))
}
```

Given the folder structure
```
assets/
	css/
		main.css
	js/
		main.js
```

a request to `/css/main.css` and `/js/main.js` will return the requested files but a request to e.g. `/css/` would return a `fs.ErrNotExist` error.
