# 03\-getitem\-test\.py<a name="DAX.client.run-application-python.03-getitem-test"></a>

The `03-getitem-test.py` program performs `GetItem` operations on *TryDaxTable*\.

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

pk = 10
sk = 10
iterations = 50

start = time.time()
for i in range(iterations):
    for ipk in range(1, pk+1):
        for isk in range(1, sk+1):
            params = {
                'TableName': table_name,
                'Key': {
                    "pk": {'N': str(ipk)},
                    "sk": {'N': str(isk)}
                }
            }

            result = client.get_item(**params)
            print('.', end='', file=sys.stdout); sys.stdout.flush()
print()

end = time.time()
print('Total time: {} ms - Avg time: {} ms'.format(end - start, (end-start)/iterations))
```