I have an already running AlloyDB Cluster configured in GCP Project gcp-poc-data-dev and I want to access the AlloyDB via VM running in GCP Project gcp-poc-apps-dev  , I cannot use Private Service Connect since the Cluster was not created with Private Service Access Option and I have setup a bastion host in Project gcp-poc-data-dev that has access to AlloyDB  instance. How can I connect to bastion host since Project gcp-poc-data-dev and Project gcp-poc-apps-dev hav different VPCs? I do not want to use VPC peering and would like to user IAP? Can you suggest gcloud command to achieve the overall objective of accessing cross project AlloyDB via IAP?

### Deployment Diagram for Cross-Project AlloyDB Access via IAP

Here is a deployment diagram that illustrates the solution using IAP for TCP forwarding to securely connect to your AlloyDB instance across projects.

```plantuml
@startuml
!theme vibrant
!define DEVICONS https://raw.githubusercontent.com/tupadr3/plantuml-icon-font-sprites/main/devicons
!define FONTAWESOME https://raw.githubusercontent.com/tupadr3/plantuml-icon-font-sprites/main/font-awesome-5
!include <DEVICONS/google.puml>
!include <FONTAWESOME/database.puml>
!include <FONTAWESOME/server.puml>
!include <FONTAWESOME/user_secret.puml>

title AlloyDB Cross-Project Access via IAP Tunnel

cloud "Google Cloud" {
    package "Project: gcp-poc-apps-dev (VPC-A)" {
        node "Application VM\n<size:12><$server>" as AppVM {
            artifact "Your Application" as App
            artifact "gcloud CLI" as GCloudCLI
        }
    }

    package "Project: gcp-poc-data-dev (VPC-B)" {
        node "Bastion Host VM\n<size:12><$server>" as BastionVM {
             artifact "AlloyDB Auth Proxy" as AuthProxy
        }
        database "AlloyDB Cluster\n<size:12><$database>" as AlloyDB
    }

    component "Identity-Aware Proxy (IAP)\n<size:12><$user_secret>" as IAP
}

' === Connection Flow ===
App -> GCloudCLI: 1. Initiates tunnel\n(gcloud compute start-iap-tunnel)
GCloudCLI --> IAP: 2. Authenticates & Authorizes\n(IAM Check)
IAP --> BastionVM: 3. Forwards TCP traffic
App -> GCloudCLI: 4. App connects to localhost port
BastionVM -> AuthProxy: 5. Traffic received from IAP
AuthProxy --> AlloyDB: 6. Securely connects to AlloyDB

' === Network Boundaries ===
BastionVM -- AlloyDB : (Private IP within VPC-B)

note right of AppVM
  The application connects to `localhost:5432`,
  which is tunneled by `gcloud` to the
  bastion host.
end note

@enduml
```
