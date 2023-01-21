# Regions Available to use for pubsub multi-region storage policy

| S.no | Description | Region |
|------|-------------|------|
| <a name="input_serial_number"></a> [1](#input_serial_number) | For South Carolina, USA as Store Region | `"us-central1"` |
| <a name="input_serial_number"></a> [2](#input_serial_number) | For N. Virginia, USA as Store Region | `"us-east4"` |
| <a name="input_serial_number"></a> [3](#input_serial_number) | For London, Europe as Store Region | `"europe-west2"` |
| <a name="input_serial_number"></a> [4](#input_serial_number) | For Frankfurt, Europe. as Store Region | `"europe-west3"` |
| <a name="input_serial_number"></a> [5](#input_serial_number) | For Mumbai, India. as a Store Region | `"asia-south1"` |
| <a name="input_serial_number"></a> [6](#input_serial_number) | For Delhi, India. as Store Region | `"asia-south2"` |
| <a name="input_serial_number"></a> [7](#input_serial_number) | For Singapore as Store Region | `"asia-southeast1"` |

# Description:

* To enable and use above storage regions please modify message storage policy in terraform example.
* For Single Region please specify any one region, for multi region storage policy please specify the regions in list format. the example is provided below

* For single-region storage policy
```tf
message_storage_policy = {
    allowed_persistence_regions = ["us-central1"] #["europe-west3"], ["europe-west2"], ["asia-southeast1"], ["asia-south1"], ["asia-south2"]
  }
```
* For multi-region storage policy
```tf
message_storage_policy = {
    allowed_persistence_regions = ["us-central1", "europe-west3", "asia-south1"] #["europe-west3"], ["europe-west2"], ["asia-southeast1"], ["asia-south1"], ["asia-south2"]
  }