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
| Software | https://github.com/containers/buildah |
| Security Provider | No |
| Languages | Go |
| SBOM | https://github.com/containers/buildah/blob/main/go.mod |

### Security links

| Doc | url |
| -- | -- |
| Security file | https://github.com/containers/buildah/blob/main/SECURITY.md |
| Project website | https://buildah.io |
| Documentation | https://github.com/containers/buildah/tree/main/docs |
| Contributing guidelines | https://github.com/containers/buildah/blob/main/CONTRIBUTING.md |

## Overview

Buildah is a tool that facilitates building Open Container Initiative (OCI) images. It provides a flexible and efficient way to create container images without requiring a running container daemon, emphasizing security through rootless builds and fine-grained control over the image creation process.

### Background

Buildah is a command-line tool designed for building OCI-compliant container images. Unlike traditional container build tools that require a daemon, Buildah operates directly as a command-line application, providing better security and flexibility.

Key characteristics:
- **Daemonless**: No background daemon process required for building images
- **Rootless builds**: Supports building images without root privileges
- **OCI-compliant**: Fully compliant with OCI image specifications
- **Dockerfile compatibility**: Can build images from Dockerfiles
- **Fine-grained control**: Provides detailed control over the build process
- **Integration**: Works seamlessly with Podman, Skopeo, and other container tools

Buildah is part of the containers ecosystem maintained by Red Hat and the open-source community.

### Actors

* **Buildah CLI**: The main command-line interface that users interact with for building container images.

* **Build context**: The filesystem context containing source code and build instructions.

* **OCI runtime**: Interfaces with OCI-compliant runtimes (runc, crun) to execute build commands when needed.

* **Image store**: Manages container images and their layers during the build process.

* **Registry client**: Handles interactions with container registries for pulling base images and pushing built images.

* **Storage backend**: Manages container storage layers and filesystems during the build process.

### Actions

* **Image building**:
  - Parses Dockerfile or receives direct build commands
  - Pulls base images from registries
  - Executes build steps in isolated environments
  - Creates image layers and metadata
  - Applies security policies during build

* **Container creation**:
  - Creates working containers for build operations
  - Sets up namespaces and cgroups for isolation
  - Configures security policies for build containers

* **Layer management**:
  - Creates and manages image layers
  - Optimizes layer size and composition
  - Handles layer caching for efficiency

* **Image inspection**:
  - Provides detailed information about images
  - Verifies image metadata and configuration
  - Checks security attributes

* **Image publishing**:
  - Pushes built images to registries
  - Handles authentication with registries
  - Supports image signing

### Goals

* **Rootless image building**: Enable users to build container images without root privileges, reducing security risks.

* **Daemonless operation**: Eliminate the need for a daemon process, reducing attack surface and improving security.

* **OCI compliance**: Maintain full compatibility with OCI specifications for container images.

* **Dockerfile compatibility**: Support standard Dockerfiles while providing enhanced security features.

* **Flexible build process**: Provide fine-grained control over the image building process.

* **Integration**: Work seamlessly with other container tools in the ecosystem.

### Non-goals

* **Container runtime**: Buildah does not run containers in production (that's handled by Podman, Docker, etc.).

* **Container orchestration**: Buildah does not provide cluster orchestration capabilities.

* **Image registry**: Buildah does not operate as a container registry, though it interacts with them.

* **Continuous integration platform**: While used in CI/CD, Buildah itself is not a CI/CD platform.

## Self-assessment use

This self-assessment is created by the Buildah team to perform an internal analysis of the
project's security.  It is not intended to provide a security audit of Buildah, or
function as an independent assessment or attestation of Buildah's security health.

This document serves to provide Buildah users with an initial understanding of
Buildah's security, where to find existing security documentation, Buildah plans for
security, and general overview of Buildah security practices, both for development of
Buildah as well as security of Buildah.

This document provides the CNCF TAG-Security with an initial understanding of Buildah
to assist in a joint-assessment, necessary for projects under incubation.  Taken
together, this document and the joint-assessment serve as a cornerstone for if and when
Buildah seeks graduation and is preparing for a security audit.

## Security functions and features

### Critical Security Components

* **Rootless builds**: Buildah's core security feature that allows building images without root privileges, significantly reducing the attack surface.

* **User namespaces**: Provides process isolation during builds by mapping container user IDs to host user IDs.

* **Build isolation**: Each build operation is isolated from the host system and other builds.

* **Daemonless architecture**: Eliminates the daemon process, reducing potential attack vectors.

* **Security policy enforcement**: Applies seccomp, SELinux, and capabilities restrictions during builds.

### Security Relevant Components

* **Image verification**: Support for verifying container image signatures before using them as base images.

* **Secure defaults**: Provides secure defaults for build operations.

* **Credential management**: Secure handling of registry credentials during image operations.

* **Layer security**: Proper handling and isolation of image layers during builds.

* **Mount security**: Secure mounting of volumes and filesystems during build operations.

## Project compliance

* **OCI Compliance**: Buildah is fully compliant with the Open Container Initiative (OCI) specifications for container images.

* **OpenSSF Best Practices**: Buildah has achieved a [passing OpenSSF Best Practices badge](https://www.bestpractices.dev/projects/10579), demonstrating adherence to security best practices.

* **SELinux**: Full integration with SELinux for mandatory access control during builds.

* **AppArmor**: Support for AppArmor profiles for additional access control.

## Secure development practices

### Development Pipeline

* **Code Review Process**: All code changes require review by at least one maintainer before merging. The project uses GitHub pull requests for all contributions.

* **Automated Testing**: Comprehensive integration test suite is run in CI on every PR and also on a nightly basis. These tests exercise the buildah binary compiled using the PR's source code and the latest HEAD commit respectively.

* **Security Scanning**: Automated vulnerability scanning of dependencies using tools like Dependabot and GitHub Security Advisories. All medium and higher severity exploitable vulnerabilities are fixed in a timely way after they are confirmed.

* **Static Analysis**: Code quality and security analysis using golangci-lint which is run on every PR, ensuring testing is done prior to merge. The tool includes rules to look for common vulnerabilities in Go code.

* **Dynamic Analysis**: Comprehensive integration test suite is run in CI on every PR and also on a nightly basis. If the integration tests point out any issues in the development phase itself, those get fixed before any code is merged.

* **Container Image Security**: Built images follow security best practices and are regularly updated for security patches.

* **OpenSSF Best Practices Compliance**: Buildah has achieved a [passing OpenSSF Best Practices badge](https://www.bestpractices.dev/projects/10579), demonstrating adherence to security best practices including proper licensing, contribution guidelines, and security processes.

### Communication Channels

* **Internal**: Team communication through Slack channels and regular maintainer meetings.

* **Inbound**:
  - GitHub Issues for bug reports and feature requests
  - GitHub Discussions for community questions
  - Security issues via GitHub Security Advisories
  - Mailing lists for formal discussions
  - Clear contribution guidelines documented in [CONTRIBUTING.md](https://github.com/containers/buildah/blob/main/CONTRIBUTING.md)

* **Outbound**:
  - Release announcements via GitHub releases
  - Security advisories through GitHub Security Advisories
  - Documentation updates and blog posts
  - Conference presentations and talks
  - Project website at [buildah.io](https://buildah.io) with comprehensive documentation

### Ecosystem

Buildah is a critical component of the cloud-native ecosystem:

* **Kubernetes Integration**: Buildah can be used in Kubernetes environments for building container images securely.

* **OpenShift**: Buildah is integrated into Red Hat OpenShift for secure image building workflows.

* **Container Ecosystem**: Integrates with Podman for running containers, Skopeo for image operations, and CRI-O for Kubernetes runtime.

* **Development Tools**: Widely used in development environments for building container images securely.

* **CI/CD Pipelines**: Used in many CI/CD systems for building containerized applications with enhanced security.

## Security issue resolution

### Responsible Disclosures Process

* **Reporting**: Security vulnerabilities should be reported through GitHub Security Advisories or by emailing the security team directly. The project maintains clear reporting channels documented in the [SECURITY.md](https://github.com/containers/buildah/blob/main/SECURITY.md) file.

* **Response Time**: The security team commits to responding to vulnerability reports within 48 hours. All medium and higher severity exploitable vulnerabilities are prioritized as a matter of general practice.

* **Coordination**: For critical vulnerabilities, the team coordinates with downstream projects and maintainers to ensure coordinated disclosure.

* **Credit**: Security researchers who responsibly disclose vulnerabilities are credited in security advisories and release notes.

* **Public Disclosure**: Vulnerabilities are disclosed through GitHub Security Advisories with appropriate embargo periods for critical issues, following industry best practices for responsible disclosure.

### Vulnerability Response Process

* **Triage**: Security reports are triaged by the security team and assigned severity levels (Critical, High, Medium, Low) using CVSS scoring where applicable.

* **Investigation**: The team investigates the vulnerability, determines impact, and develops fixes. All medium and higher severity exploitable vulnerabilities discovered through static or dynamic analysis are fixed in a timely way after they are confirmed.

* **Fix Development**: Security fixes are developed in private repositories to prevent premature disclosure. The project maintains a clear process for developing and testing security patches.

* **Testing**: Fixes undergo thorough testing including regression testing and security validation. The comprehensive integration test suite ensures that fixes don't introduce new issues.

* **Disclosure**: Vulnerabilities are disclosed through GitHub Security Advisories with appropriate embargo periods for critical issues. The project follows industry best practices for coordinated vulnerability disclosure.

* **Timeline**: All medium and higher severity exploitable vulnerabilities are fixed within 60 days of being made public, following the project's commitment to timely security updates.

### Incident Response

* **Detection**: Security incidents are detected through automated monitoring, user reports, security research, and the comprehensive testing suite that runs on every PR and nightly.

* **Assessment**: The team assesses the severity and impact of security incidents using CVSS scoring and industry-standard severity classification.

* **Containment**: Immediate steps are taken to contain and mitigate the impact of security incidents. If the integration tests point out any issues in the development phase, those get fixed before any code is merged.

* **Communication**: Affected users are notified through security advisories and release notes. The project maintains clear communication channels for security updates.

* **Recovery**: Patches and updates are released as quickly as possible to address security issues. All medium and high severity vulnerabilities are prioritized as a matter of general practice.

* **Post-Incident Review**: The team conducts post-incident reviews to identify improvements to the security process and prevent similar issues in the future.

## Appendix

### Known Issues Over Time

* **Security Advisories**: Buildah maintains a comprehensive list of security advisories at https://github.com/containers/buildah/security/advisories

* **Track Record**: The project has a strong track record of quickly addressing security issues, with most vulnerabilities being patched within days of discovery.

* **Code Review**: The project's code review process has caught numerous potential security issues before they reach production.

### OpenSSF Best Practices

* **Current Status**: Buildah has achieved a [passing OpenSSF Best Practices badge](https://www.bestpractices.dev/projects/10579) (100% compliance), demonstrating adherence to security best practices.

* **Key Achievements**:
  - Comprehensive project documentation and contribution guidelines
  - Robust security testing and analysis processes
  - Clear vulnerability disclosure and response procedures
  - Strong development practices with code review and automated testing
  - Proper licensing and project governance

* **Compliance Areas**: The project meets all MUST and MUST NOT criteria, all SHOULD criteria, and considers all SUGGESTED criteria as required by the OpenSSF Best Practices framework.

### Case Studies

* **Enterprise CI/CD**: A large enterprise uses Buildah in their CI/CD pipeline to build container images securely in rootless environments, ensuring developers cannot compromise the build infrastructure.

* **Multi-tenant Build Systems**: A cloud service provider uses Buildah to provide secure container image building for multiple tenants, leveraging rootless builds and namespace isolation.

* **Security-Conscious Organizations**: Government agencies and financial institutions use Buildah for building container images in compliance with strict security requirements.

### Related Projects / Vendors

* **Podman**: Buildah and Podman work together, with Buildah handling container image building and Podman managing container execution.

* **Skopeo**: Buildah integrates with Skopeo for container image operations like copying and signing images.

* **CRI-O**: Buildah and CRI-O are both part of the containers ecosystem, with CRI-O focusing on Kubernetes runtime.

* **Docker**: Buildah is often compared to Docker's build functionality as a more secure and flexible alternative with rootless support.

* **Kaniko**: Both Buildah and Kaniko provide daemonless container image building, but Buildah offers more flexibility and native rootless support.

