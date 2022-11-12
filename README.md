# JavaPOS Development Process

This repository hosts GitHub Action workflows used by the build projects at this organization.
The main idea is to establish common workflows used across all builds as described at [Reusing workflows](https://docs.github.com/en/actions/using-workflows/reusing-workflows).

## Development process

The development process for adapting to a new UnifiedPOS version is as follows:

1. Add the "-SNAPSHOT" suffix to every version definition at 
    - [javapos-contracts/build.gradle](https://github.com/JavaPOSWorkingGroup/javapos-contracts/tree/master/build.gradle)
    - [javapos-controls/build.gradle](https://github.com/JavaPOSWorkingGroup/javapos-controls/tree/master/build.gradle)
    - [javapos/build.gradle](https://github.com/JavaPOSWorkingGroup/javapos/tree/master/build.gradle)
1. Add new category types as control interface types respectively new properties and new method to those control interface types at [javapos-contracts/src/main/java/jpos](https://github.com/JavaPOSWorkingGroup/javapos-contracts/tree/master/src/main/java/jpos)
1. Publish the javapos-contracts library as pre-release on GitHub to [Maven Central](https://oss.sonatype.org/)'s snapshot repository ("-SNAPSHOT" suffix required!).
1. Run the [Xtend](https://www.eclipse.org/xtend/) based code generator from [javapos-code-generators](https://github.com/JavaPOSWorkingGroup/javapos-code-generators) to generate
    - service interface types at [javapos-contracts/src/main/java/jpos/services](https://github.com/JavaPOSWorkingGroup/javapos-contracts/tree/master/src/main/java/jpos/services)
    - device controls class at [javapos-controls/src/main/java/jpos](https://github.com/JavaPOSWorkingGroup/javapos-controls/tree/master/src/main/java/jpos)
    - device control JUnit tests at [javapos-controls/src/test/java/jpos](https://github.com/JavaPOSWorkingGroup/javapos-controls/tree/master/src/test/java/jpos)
1. Run the device control JUnit tests just generated.
1. Publish the javapos-controls library as pre-release on GitHub to [Maven Central](https://oss.sonatype.org/)'s snapshot repository ("-SNAPSHOT" suffix required!).
1. Check all components are working well at external integration projects.
1. Publish releases of javapos-contracts and javapos-controls on GitHub (removing the "-SNAPSHOT" suffix at both) to get them published at the corresponding staging repository at [Maven Central (Sonatype)](https://oss.sonatype.org/), check the content (especially the JAR contained MANIFEST and change-log files) and release it at the staging repository (a Nexus repository).
1. Build the aggregation library at the [javapos](https://github.com/JavaPOSWorkingGroup/javapos) repository by referencing the 2 new released components.
1. Publish a release of [javapos](https://github.com/JavaPOSWorkingGroup/javapos) to [Maven Central (Sonatype)](https://oss.sonatype.org/)'s staging repository and release it from there after verification.

Note: The component [javapos-config-loader](https://github.com/JavaPOSWorkingGroup/javapos-config-loader) is not UnifiedPOS version related and normally does not need to be adapted in case of a new UnifiedPOS version. However, the same handling needs to be applied before it can be integrated into a new build version of [javapos](https://github.com/JavaPOSWorkingGroup/javapos).

## Handling of credentials

There are 4 credentials needed for releasing components to Maven Central which are maintained at [organization's Action secrets settings page](https://github.com/organizations/JavaPOSWorkingGroup/settings/secrets/actions):

- GPG_SIGNING_KEY -- the encrypted private key for JAR signing
- GPG_SIGNING_PASSPHRASE -- the passphrase for decrypting the private key when signing JARs
- SONATYPE_USERNAME -- the [Sonatype](https://oss.sonatype.org/) user name for publishing to Maven Central
- SONATYPE_PASSWORD -- the [Sonatype](https://oss.sonatype.org/) password for publishing to Maven Central


## GitHub Action workflows

All release relevant repositories are using organization workflows across all builds as described at [Reusing workflows](https://docs.github.com/en/actions/using-workflows/reusing-workflows).

The JavaPOS organization workflows consist of 3 different workflows which get applied for different situations:

- [build.yml](https://github.com/JavaPOSWorkingGroup/javapos-workflow/blob/main/.github/workflows/build.yml) -- gets applied on each push of a branch, includes tests and JAR signing attempt and therefore requires credentials
- [check.yml](https://github.com/JavaPOSWorkingGroup/javapos-workflow/blob/main/.github/workflows/check.yml) -- gets applied on each Pull Request, for contributors; the JAR signing is skipped
- [release.yml](https://github.com/JavaPOSWorkingGroup/javapos-workflow/blob/main/.github/workflows/release.yml) -- gets applied when a released is published using GitHub's release mechanism on the repositories GibHub web site

