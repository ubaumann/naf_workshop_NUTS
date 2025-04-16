# Workshop Tools Overview

This page summarizes the essential tools and frameworks used in the workshop.


## NUTS (Network Unit Testing System)
NUTS is a Python-based testing framework designed for network environments. It extends pytest to provide structured, reusable testing for network devices using real or simulated data.

- Documentation: [https://nuts.readthedocs.io/en/latest/](https://nuts.readthedocs.io/en/latest/)
- GitHub Repo: [https://github.com/network-unit-testing-system/nuts](https://github.com/network-unit-testing-system/nuts)


## pytest
pytest is the de facto standard Python testing framework that is easy to extend and use for both unit and functional testing.

- Documentation: [https://docs.pytest.org/](https://docs.pytest.org/)


## NAPALM (Network Automation and Programmability Abstraction Layer with Multivendor support)
A Python library that provides a unified API to configure and retrieve data from network devices.

- Documentation: [https://napalm.readthedocs.io/](https://napalm.readthedocs.io/)
- Mock Driver Guide: [https://napalm.readthedocs.io/en/latest/tutorials/mock_driver.html](https://napalm.readthedocs.io/en/latest/tutorials/mock_driver.html)


## Nornir
An automation framework that uses Python to manage inventory, run tasks and process network-related workflows.

- Documentation: [https://nornir.readthedocs.io/](https://nornir.readthedocs.io/)


## Containerlab
Containerlab enables container-based networking labs on a single machine or across a distributed environment. It simplifies lab topology setup for network testing.

- Documentation: [https://containerlab.dev/](https://containerlab.dev/)


## uv
uv is a fast Python package installer and virtual environment manager.

- Documentation: [https://docs.astral.sh/uv/](https://docs.astral.sh/uv/)


## INPG Stack
A combined solution of:
- **Infrahub**: Stores network inventory data.
- **Prometheus**: Collects and stores metrics.
- **Grafana**: Visualizes and dashboards collected data.

This stack is designed to give context and observability to your network testing efforts.

- Prometheus: [https://prometheus.io/](https://prometheus.io/)
- Grafana: [https://grafana.com/](https://grafana.com/)
