# 🖥️ Run your own bitcoin node

Your own node checks every bitcoin rule itself, so you don't have to trust
anyone else's server. These guides help you set one up at home.

## 📚 Recommended guide

- 🧱 [MiniBolt](https://minibolt.minibolt.info/): build a node on a mini PC
  with an Intel or AMD processor from about 2010 onwards.

MiniBolt's own [hardware requirements](https://github.com/minibolt-guide/minibolt/blob/main/index-1/preparations.md#hardware-requirements)
ask for:

- CPU: "Intel/AMD processor (2010+ gen)"
- RAM: "2+ GB RAM (only Bitcoin), 4+ GB RAM is recommended (Bitcoin +
  Lightning + bonus)"
- Storage: "SSD SATA/NVMe 2+ TB is recommended"
- A USB pen drive or SD card of "4+ GB" to install from
- A temporary monitor or TV and a USB or PS2 keyboard for the first setup

## 🚧 Planned: DIYbitcoin archival node

Our own guide is on the way: a full archival node that keeps the whole
blockchain (no pruning), with no lightning node.

Its hardware needs will differ from MiniBolt's. Still to work out:

- 💾 How much disk the full blockchain needs today, plus room to grow
- 🧠 How much RAM makes the first sync reasonably fast
- ⏱️ Whether a 2010 era processor syncs in an acceptable time
- 🔎 Whether to add an Electrum server, and how much extra disk its index
  takes
