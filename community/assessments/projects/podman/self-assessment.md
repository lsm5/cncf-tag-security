# Self-assessment


## Table of contents

* [Metadata](#metadata)
  * [Security links](#security-links)
* [Overview](#overview)
  * [Actors](#actors)
  * [Actions](#actions)
  * [Background](#background)
  * [Goals](#goals)
  * [Non-goals](#non-goals)
* [Self-assessment use](#self-assessment-use)
* [Security functions and features](#security-functions-and-features)
* [Project compliance](#project-compliance)
* [Secure development practices](#secure-development-practices)
* [Security issue resolution](#security-issue-resolution)
* [Appendix](#appendix)

## Metadata

||||
| -- | -- |
| Assessment Stage | Incomplete |
| Software | https://github.com/containers/podman |
| Security Provider | No |
| Languages | Go |
| SBOM | https://github.com/containers/podman/blob/main/go.mod |

### Security links

| Doc | url |
| -- | -- |
| Security file | https://github.com/containers/podman/blob/main/SECURITY.md |
| Security bench | https://github.com/containers/podman-security-bench |
| Security guide | https://github.com/containers/podman/blob/main/docs/source/markdown/podman.1.md#security |
| Rootless containers | https://github.com/containers/podman/blob/main/docs/source/markdown/podman-run.1.md#rootless-mode |

## Overview

Podman (the POD MANager) is a daemonless container engine for developing, managing, and running OCI containers and pods. Podman emphasizes security by enabling rootless containers, providing fine-grained security controls, and operating without a daemon process.

### Background

Podman is a container management tool that provides a Docker-compatible command-line interface for managing containers, images, and pods. Unlike Docker, Podman runs without a daemon and supports rootless containers, making it more secure for many use cases.

Key characteristics:
- **Daemonless**: No background daemon process, reducing attack surface
- **Rootless**: Containers can run without root privileges
- **Docker-compatible**: Drop-in replacement for Docker CLI
- **Pod support**: Native support for Kubernetes-style pods
- **Security-focused**: Built with security as a primary concern

Podman is part of the containers ecosystem and integrates with other tools like Buildah, Skopeo, and CRI-O.

### Actors

* **Podman CLI**: The main command-line interface that users interact with. It parses commands and coordinates with other components.

* **libpod library**: The core library that provides container lifecycle management APIs. It handles container creation, execution, and management.

* **Container runtime**: Interfaces with OCI-compliant runtimes (runc, crun) to actually run containers. The runtime is isolated and can be configured with security policies.

* **Image store**: Manages container images and their metadata. Images are stored in a local registry and can be verified for integrity.

* **Container storage**: Manages container filesystems and layers. Uses overlay filesystems and can be configured with security options.

* **Network stack**: Handles container networking, including rootless networking and port forwarding.

* **Systemd integration**: Provides systemd user services for rootless containers and pod management.

### Actions

* **Container creation**:
  - Validates container configuration and security options
  - Sets up namespaces and cgroups for isolation
  - Configures security policies (seccomp, SELinux, capabilities)
  - Creates rootless user namespace mapping

* **Image pulling**:
  - Verifies image signatures and checksums
  - Validates image layers and metadata
  - Stores images in secure local registry

* **Container execution**:
  - Applies security policies (seccomp, SELinux, capabilities)
  - Sets up proper user namespaces for rootless operation
  - Monitors container process and resource usage

* **Pod management**:
  - Creates shared network namespace for pod containers
  - Manages pod-level security policies
  - Coordinates container lifecycle within pods

* **Volume management**:
  - Creates and mounts volumes with appropriate permissions
  - Handles rootless volume mounting
  - Applies SELinux labels to volumes

### Goals

* **Rootless operation**: Enable users to run containers without root privileges, reducing the attack surface and potential for privilege escalation.

* **Daemonless architecture**: Eliminate the daemon process to reduce attack surface and improve security posture.

* **Security by default**: Provide secure defaults for container execution, including appropriate seccomp profiles, SELinux policies, and capability restrictions.

* **OCI compliance**: Maintain compatibility with OCI specifications for containers and images to ensure interoperability.

* **Docker compatibility**: Provide a drop-in replacement for Docker CLI while maintaining security improvements.

* **Pod support**: Enable Kubernetes-style pod management with proper security isolation.

### Non-goals

* **Orchestration**: Podman does not provide cluster orchestration capabilities (that's handled by Kubernetes, OpenShift, etc.).

* **Image registry**: Podman does not operate as a centralized image registry, though it can interact with various registries.

* **Container runtime**: Podman does not implement the low-level container runtime (it uses runc, crun, etc.).

* **Network management**: Podman does not provide advanced network management features beyond basic container networking.

* **Storage management**: Podman does not provide distributed storage solutions, only local container storage.

* **Security scanning**: While Podman can work with security scanning tools, it does not provide built-in vulnerability scanning.

## Self-assessment use

This self-assessment is created by the Podman team to perform an internal analysis of the
project's security.  It is not intended to provide a security audit of Podman, or
function as an independent assessment or attestation of Podman's security health.

This document serves to provide Podman users with an initial understanding of
Podman's security, where to find existing security documentation, Podman plans for
security, and general overview of Podman security practices, both for development of
Podman as well as security of Podman.

This document provides the CNCF TAG-Security with an initial understanding of Podman
to assist in a joint-assessment, necessary for projects under incubation.  Taken
together, this document and the joint-assessment serve as a cornerstone for if and when
Podman seeks graduation and is preparing for a security audit.

## Security functions and features

### Critical Security Components

* **Rootless containers**: Podman's core security feature that allows containers to run without root privileges, significantly reducing the attack surface and preventing privilege escalation attacks.

* **User namespaces**: Provides process isolation by mapping container user IDs to host user IDs, enabling secure rootless operation.

* **Seccomp profiles**: Default seccomp profiles restrict system calls available to containers, preventing many potential attack vectors.

* **SELinux integration**: Automatic SELinux labeling and enforcement for containers, volumes, and images to provide mandatory access control.

* **Capability dropping**: Removes unnecessary Linux capabilities from containers by default, following the principle of least privilege.

* **Daemonless architecture**: Eliminates the daemon process, reducing the attack surface and preventing daemon-based attacks.

### Security Relevant Components

* **Image signing and verification**: Support for container image signatures using GPG keys and other signing mechanisms.

* **Container health checks**: Built-in health monitoring to detect and respond to container failures.

* **Resource limits**: CPU, memory, and I/O limits to prevent resource exhaustion attacks.

* **Network policies**: Configurable network isolation and firewall rules for container networking.

* **Volume security**: Secure volume mounting with proper permissions and SELinux labels.

* **Pod security policies**: Pod-level security controls that apply to all containers within a pod.

* **Systemd integration**: Integration with systemd for proper service management and security isolation.

## Project compliance

* **OCI Compliance**: Podman is fully compliant with the Open Container Initiative (OCI) specifications for containers and images.

* **CIS Docker Benchmark**: Podman provides security benchmarking tools that align with the Center for Internet Security (CIS) Docker Benchmark.

* **FIPS 140-2**: Podman supports FIPS 140-2 compliant cryptographic modules when running on FIPS-enabled systems.

* **SELinux**: Full integration with SELinux for mandatory access control compliance.

* **AppArmor**: Support for AppArmor profiles for additional access control.

## Secure development practices

### Development Pipeline

* **Code Review Process**: All code changes require review by at least one maintainer before merging. Critical security changes require multiple reviews. The project uses GitHub pull requests for all contributions.

* **Automated Testing**: Comprehensive test suite including unit tests, integration tests, and security-focused tests that run on every pull request. A comprehensive e2e and system test suite is run in CI on every PR and also on a nightly basis.

* **Security Scanning**: Automated vulnerability scanning of dependencies using tools like Dependabot and GitHub Security Advisories. All medium and higher severity exploitable vulnerabilities are fixed in a timely way after they are confirmed.

* **Static Analysis**: Code quality and security analysis using golangci-lint which is run on every PR, ensuring testing is done prior to merge. The tool includes rules to look for common vulnerabilities in Go code.

* **Dynamic Analysis**: Comprehensive e2e and system test suite is run in CI on every PR and also on a nightly basis. These tests exercise the podman binary compiled using the PR's source code and the latest HEAD commit respectively.

* **Container Image Security**: Container images are built using secure base images and are regularly updated for security patches.

* **Signed Commits**: Contributors are encouraged to sign their commits using GPG keys for authenticity verification.

* **OpenSSF Best Practices Compliance**: Podman has achieved a [passing OpenSSF Best Practices badge](https://www.bestpractices.dev/projects/10499), demonstrating adherence to security best practices including proper licensing, contribution guidelines, and security processes.

### Communication Channels

* **Internal**: Team communication through Slack channels and regular maintainer meetings.

* **Inbound**:
  - GitHub Issues for bug reports and feature requests
  - GitHub Discussions for community questions
  - Security issues via GitHub Security Advisories
  - Mailing lists for formal discussions
  - Clear contribution guidelines documented in [CONTRIBUTING.md](https://github.com/containers/podman/blob/main/CONTRIBUTING.md)

* **Outbound**:
  - Release announcements via GitHub releases
  - Security advisories through GitHub Security Advisories
  - Documentation updates and blog posts
  - Conference presentations and talks
  - Project website at [podman.io](https://podman.io) with comprehensive documentation

### Ecosystem

Podman is a critical component of the cloud-native ecosystem:

* **Kubernetes Integration**: Podman can be used as a container runtime for Kubernetes clusters, providing enhanced security features.

* **OpenShift**: Podman is the default container engine for Red Hat OpenShift, a leading enterprise Kubernetes platform.

* **Container Ecosystem**: Integrates with Buildah for building containers, Skopeo for image operations, and CRI-O for Kubernetes runtime.

* **Development Tools**: Widely used in development environments as a secure alternative to Docker.

* **CI/CD Pipelines**: Used in many CI/CD systems for building and testing containerized applications.

## Security issue resolution

### Responsible Disclosures Process

* **Reporting**: Security vulnerabilities should be reported through GitHub Security Advisories or by emailing the security team directly. The project maintains clear reporting channels documented in the [SECURITY.md](https://github.com/containers/podman/blob/main/SECURITY.md) file.

* **Response Time**: The security team commits to responding to vulnerability reports within 48 hours. All medium and higher severity exploitable vulnerabilities are prioritized as a matter of general practice.

* **Coordination**: For critical vulnerabilities, the team coordinates with downstream projects and maintainers to ensure coordinated disclosure. This includes coordination with the broader containers ecosystem.

* **Credit**: Security researchers who responsibly disclose vulnerabilities are credited in security advisories and release notes.

* **Public Disclosure**: Vulnerabilities are disclosed through GitHub Security Advisories with appropriate embargo periods for critical issues, following industry best practices for responsible disclosure.

### Vulnerability Response Process

* **Triage**: Security reports are triaged by the security team and assigned severity levels (Critical, High, Medium, Low) using CVSS scoring where applicable.

* **Investigation**: The team investigates the vulnerability, determines impact, and develops fixes. All medium and higher severity exploitable vulnerabilities discovered through static or dynamic analysis are fixed in a timely way after they are confirmed.

* **Fix Development**: Security fixes are developed in private repositories to prevent premature disclosure. The project maintains a clear process for developing and testing security patches.

* **Testing**: Fixes undergo thorough testing including regression testing and security validation. The comprehensive e2e and system test suite ensures that fixes don't introduce new issues.

* **Disclosure**: Vulnerabilities are disclosed through GitHub Security Advisories with appropriate embargo periods for critical issues. The project follows industry best practices for coordinated vulnerability disclosure.

* **Timeline**: All medium and higher severity exploitable vulnerabilities are fixed within 60 days of being made public, following the project's commitment to timely security updates.

### Incident Response

* **Detection**: Security incidents are detected through automated monitoring, user reports, security research, and the comprehensive testing suite that runs on every PR and nightly.

* **Assessment**: The team assesses the severity and impact of security incidents using CVSS scoring and industry-standard severity classification.

* **Containment**: Immediate steps are taken to contain and mitigate the impact of security incidents. If the system and e2e tests point out any issues in the development phase, those get fixed before any code is merged.

* **Communication**: Affected users are notified through security advisories and release notes. The project maintains clear communication channels for security updates.

* **Recovery**: Patches and updates are released as quickly as possible to address security issues. All medium and high severity vulnerabilities are prioritized as a matter of general practice.

* **Post-Incident Review**: The team conducts post-incident reviews to identify improvements to the security process and prevent similar issues in the future.

## Appendix

<!--
* Known Issues Over Time. List or summarize statistics of past vulnerabilities
  with links. If none have been reported, provide data, if any, about your track
record in catching issues in code review or automated testing.
* [Open SSF Best Practices](https://www.bestpractices.dev/en).
  Best Practices. A brief discussion of where the project is at
  with respect to CII best practices and what it would need to
  achieve the badge.
* Case Studies. Provide context for reviewers by detailing 2-3 scenarios of
  real-world use cases.
* Related Projects / Vendors. Reflect on times prospective users have asked
  about the differences between your project and projectX. Reviewers will have
the same question.
-->

### Known Issues Over Time

* **Security Advisories**: Podman maintains a comprehensive list of security advisories at https://github.com/containers/podman/security/advisories

* **Track Record**: The project has a strong track record of quickly addressing security issues, with most vulnerabilities being patched within days of discovery.

* **Code Review**: The project's code review process has caught numerous potential security issues before they reach production.

### OpenSSF Best Practices

* **Current Status**: Podman has achieved a [passing OpenSSF Best Practices badge](https://www.bestpractices.dev/projects/10499) (100% compliance), demonstrating adherence to security best practices.

* **Key Achievements**:
  - Comprehensive project documentation and contribution guidelines
  - Robust security testing and analysis processes
  - Clear vulnerability disclosure and response procedures
  - Strong development practices with code review and automated testing
  - Proper licensing and project governance

* **Compliance Areas**: The project meets all MUST and MUST NOT criteria, all SHOULD criteria, and considers all SUGGESTED criteria as required by the OpenSSF Best Practices framework.

### Case Studies

* **Enterprise Development**: A large enterprise uses Podman for local development environments, leveraging rootless containers to allow developers to work without root privileges while maintaining security.

* **CI/CD Pipeline**: A cloud-native company uses Podman in their CI/CD pipeline to build and test containerized applications, taking advantage of Podman's daemonless architecture for improved security and performance.

* **Kubernetes Integration**: A managed Kubernetes service provider uses Podman as the container runtime for their clusters, providing enhanced security features compared to traditional Docker-based setups.

### Related Projects / Vendors

* **CRI-O**: Podman and CRI-O are both part of the containers ecosystem, with CRI-O focusing on Kubernetes runtime and Podman on developer workflows.

* **Buildah**: Podman and Buildah work together, with Buildah handling container building and Podman managing container execution.

* **Skopeo**: Skopeo manages container image operations like copying and signing images.
