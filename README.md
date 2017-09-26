# Frinx documentation pages

#### Vendor Agnostic

By design the Frinx ODL Distribution is vendor agnostic. The ability to model data and to dynamically load java classes means that any protocol modellable in YANG is supportable. Example implementations of protocols include OpenFlow, PCEP and SNMP.

#### Model-driven

The Frinx ODL Distribution is model-driven, the network and operations performed on it are modeled in YANG. The YANG data structures are shared using operational and config datastores. The datastores are accessible via RESTCONF and NETCONF.

#### Future Proof

MD-SAL allows any application to be bundled as a service and loaded into the controller. These services can be configured and inter connected to solve complex problems and adapt to a changing network. This means the Frinx ODL Distribution is future proofed.

#### Extensible

The microservices architecture enables control of protocols, plug-ins and applications and the connection of external consumers and providers. More than 50 OpenDaylight projects extend Frinx’s network functionality by bundling services and functionality as Karaf features. Karaf features are a hierarchy of OSGI bundles, ready for plugin to Frinx‘s OSGI container.

# Table of contents

- [Usage](#usage)
- [Installation](#installation)


# Usage

[(Back to top)](#table-of-contents)

- With `-a` : `lc` prints all directories, files and dotfiles in current directory.

[SBE intro](FRINX Smart Build Engine/SBE intro.md)
