# ROS 2 Foxy ARM64 Prebuilt Binaries (incl. self-built `grid_map` suite)

This repository provides a curated set of **`.deb` packages for Ubuntu 20.04 (Focal) / ROS 2 Foxy / ARM64** to enable fast, offline-friendly deployments on ARM-based systems.

> **Note:** All `ros-foxy-grid-map*` packages were **locally built by the maintainer** for ARM64 from the upstream `grid_map` project’s **`foxy-devel`** branch. See:
>
> * Upstream repo: [https://github.com/ANYbotics/grid_map](https://github.com/ANYbotics/grid_map)
> * Branch used: [https://github.com/ANYbotics/grid_map/tree/foxy-devel](https://github.com/ANYbotics/grid_map/tree/foxy-devel)
>   All other packages here are ARM64 snapshots of official ROS 2 Foxy releases.

---

## Contents

The repository root contains these packages (high-level overview):

* **Self-built `grid_map` stack (ARM64)**

  * `ros-foxy-grid-map`, `grid-map-core`, `grid-map-cv`, `grid-map-pcl`, `grid-map-ros`,
    `grid-map-visualization`, `grid-map-rviz-plugin`, `grid-map-octomap`, `grid-map-sdf`,
    `grid-map-loader`, `grid-map-demos`, `grid-map-filters`, `grid-map-cmake-helpers`,
    `grid-map-msgs`
  * Version suffix: `2.0.0-0focal_arm64.deb`
* **Perception / PCL**

  * `ros-foxy-perception-pcl`, `ros-foxy-pcl-ros`, `ros-foxy-pcl-conversions`
* **Filters / OctoMap**

  * `ros-foxy-filters`, `ros-foxy-octomap`, `ros-foxy-octomap-msgs`
* **Nav2 (selected)**

  * `ros-foxy-nav2-common`, `ros-foxy-nav2-msgs`, `ros-foxy-nav2-util`, `ros-foxy-nav2-voxel-grid`,
    `ros-foxy-nav2-costmap-2d`
* **Connext / RMW support (optional)**

  * `rti-connext-dds-5.3.1_0.0.0-0_arm64.deb`
  * `ros-foxy-connext-cmake-module`, `ros-foxy-rmw-connext-{cpp,shared-cpp}`,
    `ros-foxy-rosidl-typesupport-connext-{c,cpp}`, `ros-foxy-rosidl-generator-dds-idl`,
    `ros-foxy-test-msgs`

> See the directory listing for the exact filenames and versions you have.

---

## Supported Platform

* **OS:** Ubuntu 20.04 (Focal)
* **ROS:** ROS 2 Foxy Fitzroy
* **Arch:** ARM64 (aarch64)
* **ABI:** Compatible with Foxy’s official ABI (the `grid_map` packages are self-built against upstream `foxy-devel` targets)

---

## Source & Build Details for `grid_map`

* **Upstream project:** ANYbotics `grid_map`

  * Repo: [https://github.com/ANYbotics/grid_map](https://github.com/ANYbotics/grid_map)
  * Branch: [https://github.com/ANYbotics/grid_map/tree/foxy-devel](https://github.com/ANYbotics/grid_map/tree/foxy-devel)
* **Build target:** Ubuntu 20.04 + ROS 2 Foxy + ARM64
* **Packaging:** Debians named `ros-foxy-grid-map*` with version suffix `2.0.0-0focal_arm64.deb` to align with the upstream 2.0.0 series for Foxy.
* **(Optional) Reproducibility note:** If you want strict reproducibility, record the exact commit used (e.g., set an environment variable `GRID_MAP_COMMIT=<commit-sha>` and tag this README with it).

---

## Quick Install

> Recommended on a **clean Foxy environment** to avoid conflicts with packages from online apt sources.
> If you require Connext, install Connext-related packages **first**.

### A) Offline, install-all (recommended for air-gapped)

```bash
cd /path/to/this/repo

# 1) (Optional) Connext/RMW
sudo dpkg -i \
  rti-connext-dds-5.3.1_0.0.0-0_arm64.deb \
  ros-foxy-connext-cmake-module_*.deb \
  ros-foxy-rmw-connext-shared-cpp_*.deb \
  ros-foxy-rmw-connext-cpp_*.deb \
  ros-foxy-rosidl-typesupport-connext-c_*.deb \
  ros-foxy-rosidl-typesupport-connext-cpp_*.deb \
  ros-foxy-rosidl-generator-dds-idl_*.deb

# 2) Install the rest (grid_map, nav2, pcl, etc.)
sudo dpkg -i *.deb || sudo apt-get -f install -y
```

If dependencies are missing, run:

```bash
sudo apt-get -f install -y
# Optionally re-run to ensure everything is configured:
sudo dpkg -i *.deb
```

### B) Selective install

For example, install only the `grid_map` stack:

```bash
sudo dpkg -i ros-foxy-grid-map*_2.0.0-0focal_arm64.deb || sudo apt-get -f install -y
```

### Use Connext (optional)

```bash
export RMW_IMPLEMENTATION=rmw_connext_cpp
ros2 doctor --report
```

> By default, Foxy uses `rmw_fastrtps_cpp`. You do not need Connext unless you specifically require it.

---

## Usage Examples

### Visualize `grid_map` demos

```bash
source /opt/ros/foxy/setup.bash
ros2 launch grid_map_demos demo.launch.py
```

### Integrate with Nav2

With `nav2-costmap-2d` and the `grid_map` packages installed, reference `grid_map` topics or layers in your Nav2 configuration as needed. This repository only provides binaries; please refer to your project’s YAML configs and launch files.

---

## Verification

It is recommended to checksum packages before deployment:

```bash
# Create checksums:
sha256sum *.deb | tee SHA256SUMS.txt

# Verify on target:
sha256sum -c SHA256SUMS.txt
```

If desired, publish `SHA256SUMS.txt` (and signatures) in this repo.

---

## Compatibility & Notes

* Intended for **Ubuntu 20.04 + ROS 2 Foxy + ARM64** only.
* Self-built `grid_map` targets `foxy-devel` / 2.0.0 ABI. Third-party forks with custom ABI may not be binary-compatible.
* Avoid conflicts with identically named packages from other apt sources; prefer testing in a clean environment or container.
* `rti-connext-dds` is a third-party, closed-source dependency and **requires proper licensing** from RTI.

---

## Troubleshooting (FAQ)

* **`dpkg: dependency problems prevent configuration`**
  Run `sudo apt-get -f install -y`, then re-run `dpkg -i`.
* **`exec format error` / wrong architecture**
  Ensure the target is **ARM64**, running **Ubuntu 20.04**.
* **Nav2 / plugin load errors**
  Ensure the environment is sourced: `source /opt/ros/foxy/setup.bash`; check `LD_LIBRARY_PATH` and `AMENT_PREFIX_PATH`.
* **Connext runtime issues**
  Export `RMW_IMPLEMENTATION=rmw_connext_cpp` and verify licensing and runtime prerequisites.

---

## Versions & Origin

* **`grid_map`**: self-built for ARM64 from **ANYbotics `grid_map` `foxy-devel`** branch (upstream repo and branch linked above). Filenames in this repo are authoritative for exact revisions.
* **Other packages**: official **Foxy/Focal/ARM64** binary snapshots from the ROS ecosystem.

---

## Contributing

Issues and PRs are welcome. When filing issues, please include:

* Device model, OS image, ROS 2 version
* Exact package filenames installed
* Full logs and repro steps

---

<!-- ## Maintainer

* **Maintainer:** *<Lucas Meng / DEEPRobotics>*
* **Contact:** *<mengliang@deeprobotics.cn>* -->

---

## Licensing

* Self-built artifacts inherit the licenses of their upstream projects (see each upstream LICENSE).
* `rti-connext-dds` and related components are governed by **RTI’s licensing terms**.
* Unless otherwise noted, repository docs/scripts are released under **Apache-2.0**.
