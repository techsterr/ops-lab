# Index lifecycleedit
ILM defines five index lifecycle phases:

- Hot: The index is actively being updated and queried.
- Warm: The index is no longer being updated but is still being queried.
- Cold: The index is no longer being updated and is queried infrequently. The information still needs to be searchable, but it’s okay if those queries are slower.
- Frozen: The index is no longer being updated and is queried rarely. The information still needs to be searchable, but it’s okay if those queries are extremely slow.
- Delete: The index is no longer needed and can safely be removed.


# Start Elasticsearch and Kibana Server
```
docker-compose up
```


# Go to Kibana

```
http://localhost:5601
```

# Create index

```
http://localhost:5601/app/dev_tools#/console

PUT /test-index
```

# In Progress