# Using Known Vulnerable Components
- subscribe to cyber-security related mailing lists from Information Security (InfoSec) sites and software vendors and regularly search vulnerability databases

## Component tracking and management
Track and catalog all software components in your application to gain visibility into dependencies and ensure they are properly managed. This foundational step helps identify potential risks and simplifies the process of mitigating vulnerabilities.

This can be typically achieved with a **Software Bill of Materials (SBOM)**. An SBOM is an essential tool for tracking and managing third-party dependencies in a project. A SBOM is a formal set of machine-readable metadata describing your software application. SBOM is a listing of all componentes, their licenses, verison and origin.

## Proactive vulnerability detection
Implement automated tools, such as Software Composition Analysis (SCA), to continuously scan dependencies for known vulnerabilities. This ensures early detection and prioritization of risks based on severity and impact.

**Software Composition Analysis (SCA)** tools have the flexibility to both generate and utilize Software Bill of Materials (SBOMs). Many SCA tools automatically create SBOMs as part of their analysis process, scanning the codebase and dependency manifests (e.g., package.json, pom.xml) to catalog all direct and transitive dependencies. Additionally, SCA tools can consume pre-existing SBOMs to analyze dependencies and assess risks.

Sample open source tools
OWASP Dependency-Check: An open-source tool that identifies known vulnerabilities in project dependencies using the National Vulnerability Database (NVD).

## .NET Core
### Versioning
The version numbering scheme used in C# consists of four components: major version, minor version, build number, and revision. For example, "1.0.0.0" or "1.1.12345.0".

The major version number indicates a major release or significant change in the application, such as new features.
The minor version indicates minor updates that do not make significant changes in the application.
The build version is incremented each release and is used to identify a specific build of the software.
The revision number is incremented with each change made to the build.
NuGet
The version of the components that install with NuGet and their version numbers can be found in the project's .csproj file. When no version is provided for the component in the file, NuGet will install the latest version of the library.

When a NuGet package is added to a project, NuGet generates a file called packages.lock.json (for projects using PackageReference) or packages.config.lock (for projects using packages.config) to track the exact versions of all installed packages and their dependencies. However, for the packages.lock.json file to be generated, the RestorePackagesWithLockFile property must be explicitly set to true in the .csproj file. This ensures that the lock file is created, enabling consistent package versions across different environments, such as development, testing, and production. Additionally, the lock file helps protect against OSS (Open Source Software) supply chain injection attacks by including a hash of each package's archive to verify its integrity.

### Dependency scanning
#### NuGet
NuGet has a built-in feature for scanning for vulnerable packages.

##### Vulnerable packages
The following command lists all the installed packages that have known vulnerabilities:

`dotnet list package --vulnerable`
To include transitive dependencies (dependencies of dependencies) in the scan, the --include-transitive flag can be used:

`dotnet list package --vulnerable --include-transitive`
The command will output a list of all the installed packages that have known vulnerabilities, along with information about the severity of the vulnerability and any available updates.

##### Outdated packages
To list all packages that have a new stable version, unless the resolved version is also a prerelease version, available. The --outdated flag can be used:

`dotnet list package --outdated`
Update packages
Vulnerable or outdated packages can be updated with the command:

`dotnet update PackageName`

##### Alternatives
Additionally, there are a number of other tools available as well. Another would be:

OWASP Dependency Check, which can scan dependencies in .NET, among other languages for known vulnerabilities.

---
