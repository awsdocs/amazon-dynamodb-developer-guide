# Examples of Sparse Indexes in DynamoDB<a name="bp-indexes-sparse-examples"></a>

Global secondary indexes are sparse by default\. When you create a global secondary index, you specify a partition key and optionally a sort\-key\. Only items in the parent table that contain those attributes appear in the index\.

By designing a global secondary index to be sparse, you can provision it with lower write throughput than that of the parent table, while still achieving excellent performance\.

For example, a gaming application might track all scores of every user, but generally only needs to query a few high scores\. The following design handles this scenario efficiently:

![\[Sparse GSI example.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/SparseIndex_A.png)

Here, Rick has played three games and achieved `Champ` status in one of them\. Padma has played four games and achieved `Champ` status in two of them\. Notice that the `Award` attribute is present only in items where the user achieved an award\. The associated global secondary index looks like the following:

![\[Sparse GSI example.\]](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/images/SparseIndex_B.png)

The global secondary index contains only the high scores that are frequently queried, which are a small subset of the items in the parent table\.