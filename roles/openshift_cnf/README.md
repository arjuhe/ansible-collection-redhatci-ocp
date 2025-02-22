# Automate creation of the Openshift-cnf project for vendor validated

This role will automatically generate an Openshift-cnf certification project when the option `create_cnf_project` is set to `true`. The new role reuses some tasks from the `create_certification_project` role, and the associated templates are stored within this role. Currently, there are no mandatory parameters that need to be updated in the new role.

Please note that the role `openshift_cnf` is currently a basic automation setup. It will undergo further updates once additional options are available by the backend REST API, such as automatic start/continue parameters for starting `Certify the functionality of your CNF on Red Hat OpenShift` step inside the project.

The current backend behavior for attaching a product listing to newly created cert projects requires to include the <old_certs_list> and <new_cert_list>. This is because the product listing ID may have been used in both the old and new projects. To address this, we implemented an enhancement to query all old cert projects based on the product listing ID that was used in both lists. We then merge the results into a single array and attach the product listing ID to to all cert projects.

## Global Variables
As the new role openshift_cnf reuses some existing tasks, please refer to the description in the `create_certification_project` role for information on the shared global variables.


## Variables to define for each cnf_to_certify

Name                     | Default                                                                    | Description
-------------------      | ------------                                                               | -------------
create_cnf_project       | false                                                                      | If set to true, it would create a new Openshift-cnf certification project.
cnf_name                 | None                                                                       | If defined, it would create Openshift-cnf certification project for vendor validated, cnf_name format: `CNF25.8 + OCP4.12`


## Variables to define for project settings under `cert_settings`

Name                          | Default                              | Description
----------------------------- | ------------------------------------ | -------------
email_address                 | "mail@example.com"                   | String. Email address is needed for creating openshift-cnf project


## Variables to define for project settings under `cert_listings` main variable

Name                          | Default                              | Description
----------------------------- | ------------------------------------ | -------------
pyxis_product_lists           | None                                 | A list of Product Listings; all of them must be created beforehand [See doc](https://redhat-connect.gitbook.io/red-hat-partner-connect-general-guide/managing-your-account/product-listing). It could contain one or many PLs. If set, it will attach all PLs to both old and new certification projects.
published                     | false                                | Boolean to enable publishing list of products
type                          | "container stack"                    | String. Type of product list


## Example Configuration of Openshift-cnf certification project creation
```yaml
---
dci_topic: OCP-4.11
dci_name: Testing Openshift-cnf auto creation and attach
dci_configuration: Using DCI create cnf project and attach product-list
check_for_existing_projects: true
dci_config_dirs: [/etc/dci-openshift-agent]
partner_creds: "/var/lib/dci-openshift-app-agent/auth.json"
organization_id: 12345678
#cnf_name is a free-text but format: CNF-version + OCP-version e.g "CNF23.5 OCP4.12.9"
cnf_to_certify:
  - cnf_name: "test-smf23.5 OCP4.11.5"
    create_cnf_project: true

  - cnf_name: "test-upf23.5 OCP4.11.5"
    create_cnf_project: true

cert_settings:
  email_address: "email@example.com"

cert_listings:
  published: false
  type: "container stack"
  pyxis_product_lists:
    - "xxxxxxxxxxxxxxxxxxxxxxxx"
    - "yyyyyyyyyyyyyyyyyyyyyyyy"

pyxis_apikey_path: "/var/lib/dci-openshift-app-agent/pyxis-apikey.txt"
dci_gits_to_components: []
...
```