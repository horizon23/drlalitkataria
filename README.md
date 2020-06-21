## Dr Lalit Mohan Kataria

<p style="text-align:justify;">It's a very common problem across whole data industry, people are struggling to reduce the time complexity, they bought big machines, they moved to spark but yet, there are few problems which can't be solved by just using something, it requires a deeper insight to literally feel how the framework works and why it's very slow even with considerable amount of resources.</p>
<p style="text-align:justify;">Here we'll be discussing how spark treats a join and figure out how to join.</p>
<p style="text-align:justify;"><!--more--></p>
<p style="text-align:justify;">Before we go into much detail, let's discuss about varies strategies which spark uses to join.</p>
<p style="text-align:justify;">There are 3 types of strategies:</p>

<ol style="text-align:justify;">
 	<li><strong>Sort-merge Join:&nbsp;</strong>The keys from both the sides are being sorted and then merged and then data is <strong>shuffled</strong> to bring down the same set of keys on same machine so that joining can be done.
<img class=" size-full wp-image-371 aligncenter" src="https://kshitijkuls.files.wordpress.com/2019/09/screenshot-2019-09-26-at-10.08.08-am.png" alt="Screenshot 2019-09-26 at 10.08.08 AM.png" width="1046" height="1378"></li>
 	<li><strong>Broadcast-hash Join:&nbsp;</strong>The smallest side is being broadcasted to each node wherever the largest side of data partition resides, then the joining takes place, this eliminates the shuffling process for bigger dataset.<img class="alignnone size-full wp-image-372" src="https://kshitijkuls.files.wordpress.com/2019/09/spark_broadcast.png" alt="spark_broadcast.png" width="528" height="797"></li>
 	<li><strong>Shuffle-hash Join:</strong> This is similar to the join in map reduce, it doesn't sort the data, it simply hash the keys of both datasets and shuffle the data to the nodes by <strong>(hash mod no. of executors)</strong>.</li>
</ol>
<p style="text-align:justify;">When both side datasets are too big, then it's better to use <strong>Sort-merge join</strong> or <strong>Shuffle-hash join</strong> depending on the data locality.</p>
<p style="text-align:justify;">After spark 2.3 release, spark made Sort merge join as the default strategy which can be disabled by setting ‘<strong class="ko ma">spark.sql.join.preferSortMergeJoin</strong>’ = <strong>false</strong>.</p>
<p style="text-align:justify;"><strong>Broadcast-hash Join</strong></p>
Best suitable when one of the dataset is small [about 1 GB]. There are 2 way of achieving this:
<ol>
 	<li>By setting <strong class="ko ma">spark.sql.autoBroadcastJoinThreshold</strong>, this parameter takes value in bytes and default is 10 MB, spark check the size of dataset and it found it to be lesser than the threshold value then it will broadcast this dataset automatically to all the nodes wherever big dataset partitions reside.<strong>**</strong> Sometimes it may happen that spark may not be able to figure out the size of data correctly, which may end up in <strong>sort-merge join</strong>, so to get rid if this, one can <strong>persist</strong> the dataset on <strong>disk and memory,</strong> by doing this spark will know the actual size of data and will choose the strategy accordingly.</li>
 	<li><strong>Forcing spark to broadcast</strong>, spark has also provided a functionality to broadcast a dataset forcefully, this approach is advisable only when we are sure about the data sizing.</li>
</ol>
&nbsp;
