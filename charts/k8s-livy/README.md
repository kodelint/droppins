# Apache Livy

* [Livy](https://livy.apache.org/)

## Chart Details
* 1 x Livy pod (Optional) with port `8998` exposed on an external `LoadBalancer`, with `HorizontalPodAutoscaler` to scale to max 10 pods when CPU hits 50%.
