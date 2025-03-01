# Fedimint UI Projects

<p align="center">
<img width="42%" alt="268047037-d648b83d-b676-44fe-98be-07f84f4d7465" src="https://github.com/fedimint/ui/assets/101964499/2d85544d-e1d0-4e06-a610-e2bc618f463b">
<img width="47%" alt="268046987-3b480175-7f5e-4235-bed3-cab5c092c2e5" src="https://github.com/fedimint/ui/assets/101964499/ce0fe039-9260-4443-9d84-9359bdfa901f">
</p>

## What's Inside

This project includes the following apps / packages:

### Apps

- `guardian-ui`: Web app experience for setting up and administering fedimints. This is used by the Fedimint guardians
- `gateway-ui`: Web app experience for managing Fedimint gateways. This is used by Gateway administrators

### Packages

- `ui`: Shared React UI component library for building Fedimint UI experiences
- `utils`: Shared utility functions like the current translation framework on Fedimint UI apps
- `eslint-config`: Shared `eslint` configurations (includes `eslint-plugin-react` and `eslint-config-prettier`)
- `tsconfig`: Shared `tsconfig.json`s used throughout Fedimint UI apps

## Version Policy

Fedimint UI releases use semantic versioning (`major.minor.patch`)

- Major and minor versions of `fedimint/ui` are made to be compatible major and minor versions of `fedimint/fedimint`
  - For instance, any `1.1.x` release of the UI should work with any `1.1.x` release of Fedimint
- Patch versions of `fedimint/ui` are made independent of `fedimint/fedimint`
  - For instance, you could run `fedimint/ui@1.0.1` against `fedimint/fedimint@1.0.0` and vice versa
  - It is always recommended to run the latest patch release of any version as they may contain important fixes
- The `master` branch of `fedimint/ui` attempts to track `master` of `fedimint/fedimint`
  - This tracking is a best effort, and sometimes the two will fall out of sync. Feel free to open an issue if you notice an incompatibility.
  - It is not recommended to run `master` in production as breaking changes may occur without warning

## Running a Release

### Build and Run from Source

#### Guardian UI

```bash
git clone git@github.com:fedimint/ui.git fedimint-ui
cd fedimint-ui/apps/guardian-ui
yarn install
PORT=3000 REACT_APP_FM_CONFIG_API="ws://127.0.0.1:18174" yarn build && yarn start
```

Replace PORT with a port of your choice, and REACT_APP_FM_CONFIG_API with the domain and port of your fedimintd API.

#### Gateway UI

```bash
git clone git@github.com:fedimint/ui.git fedimint-ui
cd fedimint-ui/apps/gateway-ui
yarn install
PORT=3001 REACT_APP_FM_GATEWAY_API="http://127.0.0.1:8175" REACT_APP_FM_GATEWAY_PASSWORD="yourpassword" yarn build && yarn start
```

### Run with Docker

**Note:** Docker images are only built for `linux/amd64`. Your docker will need to support virtualization to run on other platforms.

#### Guardian UI

The guardian UI container is available at [`fedimintui/guardian-ui`](https://hub.docker.com/r/fedimintui/guardian-ui)

```sh
docker pull fedimintui/guardian-ui:0.1.0
docker run \
  --platform linux/amd64 \
  --env "REACT_APP_FM_CONFIG_API='ws://127.0.0.1:18174'" \
  -p 3000:3000 \
  fedimintui/guardian-ui:0.1.0
```

Replace `-p 3000:3000` with a port of your choice, and `REACT_APP_FM_CONFIG_API` with the domain and port of your fedimintd API.

#### Gateway UI

The gateway UI container is available at [`fedimintui/gateway-ui`](https://hub.docker.com/r/fedimintui/gateway-ui)

```sh
docker pull fedimintui/gateway-ui:0.1.0
docker run \
  --platform linux/amd64 \
  --env "REACT_APP_FM_GATEWAY_API='ws://127.0.0.1:8175'" \
  --env REACT_APP_FM_GATEWAY_PASSWORD=password \
  -p 3001:3000 \
  fedimintui/gateway-ui:0.1.0
```

Replace `-p 3001:3000` with a port of your choice, `REACT_APP_FM_GATEWAY_API` with the domain and port of your gatewayd API, and REACT_APP_FM_GATEWAY_PASSWORD with the password you set your gateway up with.

## Development

From root repo directory:

1. Ensure Docker and yarn/nodejs are installed.
1. Run `docker compose up` (brings up a 2 server Fedimint)
1. `yarn install` (First time only)
1. `yarn build` (Needs to be rerun when code in `packages` change)
1. You can run any of the following commands during development

- `yarn dev` - Starts development servers and file watchers for all apps and packages
  - Due to port conflicts, there are dev commands for each app to run individually
    - `yarn dev:gateway-ui`
    - `yarn dev:guardian-ui`
- `yarn test` - Tests all apps and packages in the project
- `yarn build` - Build all apps and packages in the project
- `yarn clean` - Cleans previous build outputs from all apps and packages in the project
- `yarn format` - Fixes formatting in all apps and packages in the project

### Running the Fedimint Config again

After running through the config setup UI flow once, you will need to delete the `fedimintd` data to run through it again. To do this, delete the `fm_1`, `fm_2`, `fm_3`, and `fm_4` folder from the repo. These are data directories mounted to Docker containers running fedmintd and are listed in `.gitignore` so are safe to remove.

### Running with mprocs

1. Install [mprocs](https://github.com/pvolok/mprocs)
1. Run `mprocs -c mprocs.yml`

After running this command, you'll be present with the mprocs display. Along the left side are the list of processes currently available by mprocs. Along the bottom are hotkeys for navigating/interacting with mprocs. The main pane shows the shell output for the currently selected process.

The `start-servers` process shows combined logging for `fedimintd`, `bitcoind`, `gatewayd`, and `lnd`.

The `stop-servers` process can be used to stop all docker containers by hitting the `s` key to start the process. After doing so, you can return to the `start-servers` process and hit `r` to restart things.

The `guardian-ui-1` and `guardian-ui-2` are instances of `guardian-ui` apps, each running on different ports and connected to a unique `fedimintd` server instance (running in the `start-servers` process).

The `gateway-ui` is an instance of `gateway-ui` app, connected to a `gatewayd` server instance (running in the `start-servers` process).

You can see more details by viewing the `mprocs.yml` file.

### Running with local Fedimint

The docker containers and devimint are for specific releases or commits of `fedimint/fedimint`. If you would like to run the UIs against a particular version of fedimint, or using changes you have made locally to fedimint itself:

1. Run `cargo build` in fedimint
2. Run `env DEVIMINT_PATH=$(realpath ../fedimint/target/debug) yarn nix-guardian` (assuming that you have `ui` and `fedimint` repos checked out in the same directory)

This will put binaries in `fedimint/target/debug` at the front of your `$PATH`. Devimint will use these binaries instead of the ones installed via Nix.
