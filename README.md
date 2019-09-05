Google Compute Engine Cluster Strategy
===

[![Hex.pm](https://img.shields.io/hexpm/v/libclusetr_gcp_strategy.svg)](https://hex.pm/packages/libclusetr_gcp_strategy)

This Cluster Strategy can be used with [libcluster](https://github.com/bitwalker/libcluster) to automatically connect nodes running on GCE instances within the same Instance Group.

You can follow the [GCP tutorial](https://cloud.google.com/community/tutorials/elixir-phoenix-on-google-compute-engine) for setting up an instance group for Elixir.  Once up and running, this strategy uses the instance metadata HTTP endpoint and the Google Cloud API to discover other instances within the instance group and will automatically connect with any it finds.

Usage
---

Add the `libclusetr_gcp_strategy` package to your dependencies:

```elixir
  defp deps do
    [
      {:libcluster_gce_strategy, "~> 0.1"}
    ]
  end
```

and add the Cluster Supervisor to your application's supervision tree.

```elixir
defmodule MyApp.Application do
  use Application
  require Logger
  
  def start(_type, _args) do
    topologies = [
      gce: [
        strategy: Cluster.Strategy.GoogleComputeEngine,
        config: [
          release_name: :myapp
        ]
      ]
    ]
  
    children = [
      MyAppWeb.Endpoint,
      {Cluster.Supervisor, [topologies, [name: Ding.ClusterSupervisor]]}
    ]

    Supervisor.start_link(children, [strategy: :one_for_one, name: MyApp.Supervisor])
  end
end
```

Notes
---

This implementation was heavily influenced by the [GAE Implementation](https://github.com/alexgaribay/libcluster_gae) by [@alexgaribay](https://github.com/alexgaribay).