# 05\-scan\-test\.py<a name="DAX.client.run-application-python.05-scan-test"></a>

The `05-scan-test.py` program performs `Scan` operations on *TryDaxTable*\.

```
#!/usr/bin/env python
from __future__ import print_function

import os, sys, time
import amazondax
import botocore.session

region = os.environ.get('AWS_DEFAULT_REGION', 'us-west-2')

session = botocore.session.get_session()
dynamodb = session.create_client('dynamodb', region_name=region) # low-level client

table_name = "TryDaxTable"

if len(sys.argv) > 1:
    endpoint = sys.argv[1]
    dax = amazondax.AmazonDaxClient(session, region_name=region, endpoints=[endpoint])
    client = dax
else:
    client = dynamodb

iterations = 5

params = {
    'TableName': table_name
}

for i in range(iterations):
    start = time.time()
    result = client.scan(**params)

end = time.time()
print('Total time: {} ms - Avg time: {} ms'.format(end - start, (end-start)/iterations))
```