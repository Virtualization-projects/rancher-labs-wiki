
## Managing go `imports`:
Group the imports into two sections separated by a new line. The first section should have the standard go library imports and the second section should have the ones imported from other sources.

For example:
```
import (
	"net/http"
	"fmt"

	"github.com/sirupsen/logrus"
	"k8s.io/client-go/kubernetes"
	"github.com/rancher/types/apis/management.cattle.io/v3"
)
```

## Controllers:

- Use only one Controller per file.
- Comment your Sync or Create/Updated/Delete functions. Please explain at a high level and in plain english what the controller does. **Note:** There is no prize for writing cryptic comments!
- If there is a need for common functions, place them in `util.go` file in the folder of your controller. NO `utils` package please!

## Newline usage policy:

- One newline between functions
- No new line immediately after the function signature line.