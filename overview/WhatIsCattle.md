There are many reference throughout the documentation, configuration, and code to Cattle or Cattle.io.  [Cattle](https://github.com/rancherio/cattle) is the core orchestration engine of Rancher.  It is the high level component written in Java that is essentially the main loop of the whole system.

Cattle is responsible for tracking meta data, resources, relationships, states, state changes, and initiating operations.  As much as possible the real business logic of doing an operation such as "create a volume" is delegated to a different component.  Cattle is intended to be a high level orchestrator.  Most of the actual logic for operations will be found in Python, Go, or shell scripts.

Cattle also serves the main user facing API for Rancher.  As such, the validation logic for the API will be found in Cattle.
