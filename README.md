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
        run: sudo apt-get install -y libnetcdf-dev libnetcdff-dev
          liblapack-dev libopenblas-dev

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
        run: sudo apt-get install -y libnetcdf-dev libnetcdff-dev
          liblapack-dev libopenblas-dev libopenmpi-dev openmpi-bin

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
