# gha-esmf

This repository builds ESMF using GitHub Actions Linux runners (`ubuntu-24.04`)
and publishes the resulting `ESMF_DIR`s.
Standard runner compilers and APT packages for ESMF's dependencies are used.
This allows a project that depends on ESMF to be quickly built in a GitHub Actions workflow.

## Usage

No-MPI (`mpiuni`) example:

```yaml
jobs:
  build:
    runs-on: ubuntu-24.04
    steps:
      - name: Check out
        uses: actions/checkout@v4

      - name: Install dependencies
        run: sudo apt-get update && sudo apt-get install -y
          libnetcdf-dev libnetcdff-dev liblapack-dev libopenblas-dev

      - name: Fetch pre-built ESMF
        run: |
          esmf=8.4.2-gcc-13-mpiuni

          ESMF_DIR=$HOME/esmf/$esmf
          mkdir -p $ESMF_DIR
          cd $ESMF_DIR
          wget https://github.com/noaa-oar-arl/gha-esmf/releases/download/v0.0.9/${esmf}.tar.gz
          tar xzvf ${esmf}.tar.gz

          echo "ESMFMKFILE=${ESMF_DIR}/lib/libO/Linux.gfortran.64.mpiuni.default/esmf.mk" >> "$GITHUB_ENV"

      - name: Configure
        run: FC=gfortran-13 cmake -S . -B build

      - name: Build
        run: cmake --build build
```

MPI (Open MPI) example:

```yaml
jobs:
  build:
    runs-on: ubuntu-24.04
    steps:
      - name: Check out
        uses: actions/checkout@v4

      - name: Install dependencies
        run: sudo apt-get update && sudo apt-get install -y
          libnetcdf-dev libnetcdff-dev liblapack-dev libopenblas-dev
          libopenmpi-dev openmpi-bin

      - name: Fetch pre-built ESMF
        run: |
          esmf=8.4.2-gcc-13-mpi

          ESMF_DIR=$HOME/esmf/$esmf
          mkdir -p $ESMF_DIR
          cd $ESMF_DIR
          wget https://github.com/noaa-oar-arl/gha-esmf/releases/download/v0.0.9/${esmf}.tar.gz
          tar xzvf ${esmf}.tar.gz

          echo "ESMFMKFILE=${ESMF_DIR}/lib/libO/Linux.gfortran.64.mpi.default/esmf.mk" >> "$GITHUB_ENV"

      - name: Configure
        run: FC=gfortran-13 cmake -S . -B build

      - name: Build
        run: cmake --build build
```

> [!NOTE]
>
> Note the slight difference in `esmf.mk` path between MPI and no-MPI
> (`.mpiuni.default` vs. `.mpi.default`).

## Disclaimer

This repository is a scientific product and is not official communication of the National Oceanic and Atmospheric Administration, or the United States Department of Commerce. All NOAA GitHub project code is provided on an "as is" basis and the user assumes responsibility for its use. Any claims against the Department of Commerce or Department of Commerce bureaus stemming from the use of this GitHub project will be governed by all applicable Federal law. Any reference to specific commercial products, processes, or services by service mark, trademark, manufacturer, or otherwise, does not constitute or imply their endorsement, recommendation or favoring by the Department of Commerce. The Department of Commerce seal and logo, or the seal and logo of a DOC bureau, shall not be used in any manner to imply endorsement of any commercial product or activity by DOC or the United States Government.
