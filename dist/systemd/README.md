# systemd unit for the Mesh node daemon

Reference user-level unit file for running `mesh node start` under systemd, so
the daemon comes back after reboot and restarts on crash.

## Prerequisites

- The `mesh` binary is on your `PATH` (the unit assumes `~/.local/bin/mesh` —
  edit `ExecStart=` if yours lives elsewhere; `command -v mesh` will tell you).
- You've registered the node once with `mesh node register`.

## Install (user-level, recommended)

```sh
mkdir -p ~/.config/systemd/user
curl -fsSL https://raw.githubusercontent.com/projectmesh-io/mesh/main/dist/systemd/mesh-node.service \
  -o ~/.config/systemd/user/mesh-node.service

systemctl --user daemon-reload
systemctl --user enable --now mesh-node.service
```

So the daemon keeps running when you log out / survives reboot:

```sh
sudo loginctl enable-linger "$USER"
```

## Manage

```sh
systemctl --user status mesh-node
systemctl --user restart mesh-node
journalctl --user -u mesh-node -f
```

## System-level install

If you'd rather run as a dedicated system user, drop the file at
`/etc/systemd/system/mesh-node.service`, change `%h` to an absolute path, add
`User=mesh`, swap `WantedBy=default.target` for `multi-user.target`, then
`sudo systemctl enable --now mesh-node`.
