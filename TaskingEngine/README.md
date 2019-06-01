# The Tasking Engine

The tasking enginee is responsible for gathering metrics and making decisions based on those metrics. It is intended to be isolated from the input as well as the output. The initial version will output generic action messages to a Kafka queue and will read statistics from a SQLLite database.

The statistics stored in the SQLite database will be quite specific and the rules that process those statistics will be hard coded. A later version could genericise these rules.

## Implementation

### Steps

1. Review Kafka interface to see what stats can be gather and design schema, with a generic view, to store data
2. Implement rules and action outputs
3. Write Kafka interface to populate SQLite db

### Later Considerations

1. Expose a liveliness interface on the container for Kubernetes.

