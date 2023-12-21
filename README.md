<!-- TABLE OF CONTENTS -->
<details>
  <summary>Table of Contents</summary>
  <ol>
    <li>
      <a href="#about-the-project">About The Project</a>
      <ul>
        <li><a href="#built-with">Built With</a></li>
      </ul>
    </li>
    <li><a href="#roadmap">Roadmap</a></li>
    <li>
      <a href="#getting-started">Getting Started</a>
      <ul>
        <li><a href="#truenas-scale">TrueNAS Scale</a></li>
        <li><a href="#graphite_exporter">graphite_exporter</a></li>
      </ul>
    </li>
    <li><a href="#usage">Usage</a></li>
    <li>
      <a href="#exposed-metrics">Exposed metrics</a>
      <ul>
        <li><a href="#common-labels">Common labels</a></li>
        <li><a href="#memory">Memory</a></li>
      </ul>
    </li>
    <li><a href="#contributing">Contributing</a></li>
  </ol>
</details>

# About The Project

The goal was to get the metrics from the TrueNAS reporting tap inside my normal monitoring stack which consists of prometheus and grafana. In earlier versions of TrueNAS this was possible by utilising the `collectd` graphite push service to push the metrics to a `graphite_exporter` instance which then converts the metrics to prometheus metrics based on the provided mapping file. In TrueNAS scale 23.10 the reporting system was changed to `netdata` and was missing the export feature all along. In `23.10.1` the export mechanism was added back in still lacking the ability to directly export the metrics to promeheus but still in the graphite form. But the metrics format changed due to the tool change.

The goal of this small repository is to provide you with a new `graphite_mapping.conf` which is suitable to be used with the new metrics exposed by TrueNAS. This mapping file is in active development and may change regularly in the near future. Besides the raw mapping file a based grafana dashboard nad a quick summary of the required settings in TrueNAS are also included.

### Built With

* [graphite_exporter](https://github.com/prometheus/graphite_exporter)
* [grafana](https://github.com/grafana/grafana)
* [TrueNAS Scale 23.10](https://www.truenas.com/truenas-scale/)

<!-- ROADMAP -->
## Roadmap

- [x] Prepare environment
- [x] Getting basic metrics extraction working
- [x] Documentation
    - [x] Start of git project
    - [ ] Fully detailed list of all metrics
    - [ ] Multiple dashboards with pictures
- [ ] Convert all metrics to prometheus metrics without catch all rules
- [ ] Unify all metrics to a similar schema

See the [open issues]([https://github.com/github_username/repo_name](https://github.com/Supporterino/truenas-graphite-to-prometheus)/issues) for a full list of proposed features (and known issues).

<!-- GETTING STARTED -->
## Getting Started

### TrueNAS Scale

Inside of TrueNAS you have to open the `Reporting` tab and then click the `Exporters` button to adjust the already present graphite exporter by hitting the edit icon.
![Screenshot 2023-12-20 at 22 38 49](https://github.com/Supporterino/truenas-graphite-to-prometheus/assets/25184990/228eec2b-e612-4772-8bde-440ac3bcaef4)
Once in the edit view you have to adjust three fields to match your later desired metrics.
* The prefix for the graphite metrics need to be set to `truenas` for the mapping file to work
* The hostname field should be choosen according to your needs it will later populate the `instance` label of your metrics
* The update every field should match your scrape time

![Screenshot 2023-12-20 at 22 40 14](https://github.com/Supporterino/truenas-graphite-to-prometheus/assets/25184990/5a6cfd79-42ae-4173-bee5-42bb1d43d7b9)

The destination ip and port need to be set to target your `graphite_exporter` I won't cover the setup process of this tool since it was already present for me. Feel free to open a PR with an recommended install method.

### graphite_exporter

You obviously need a running `graphite_exporter` which is scrabed by your prometheus instance and is reachable by your TrueNAS instance to push metrics to.

<!-- USAGE EXAMPLES -->
## Usage

To utilise the provided `graphite_mapping.conf` replace your existing conf of your `graphite_exporter` and restart it. For the grafana dashboards you can simply import the provided `json` file.

<!-- EXPOSED METRICS -->
## Exposed metrics

The graphite metrics are getting transformed into the following prometheus metrics. The metrics are grouped by their source and each metrics is explained with their labels and usecase.

### Common labels

All metrics share a pair of common labels. The first label is the `job` label which is set to `truenas` for each metric to identify the metrics from TrueNAS. The second label is the `instance` label. The value of the is directly related to the `hostname` field from the [Getting Started](#getting-started) section and can be used to identify the host from which the metrics come. This is useful once you have multiple TrueNAS instances.

### Memory

Both memeory statistics from TrueNAS are exported on the onside the physical memory statistics and the in depth memory statistics which translates to the following metrics.

|Metric name|Labels|Value|Description|
|-----------|------|-----|-----------|
|`physical_memory`|`kind` defines the memory type like free, used, etc...|`bytes`|The `physical_memory` metric shows the basic usage of the actual physical memory.|
|`swap`|`kind` defines the type of free or used|`bytes`|Shows the statistic about the swap. The free value shows the actual reserved swap of the system.|

<!-- CONTRIBUTING -->
## Contributing

Contributions are what make the open source community such an amazing place to learn, inspire, and create. Any contributions you make are **greatly appreciated**.

If you have a suggestion that would make this better, please fork the repo and create a pull request. You can also simply open an issue with the tag "enhancement".
Don't forget to give the project a star! Thanks again!

1. Fork the Project
2. Create your Feature Branch (`git checkout -b feature/AmazingFeature`)
3. Commit your Changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the Branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request
