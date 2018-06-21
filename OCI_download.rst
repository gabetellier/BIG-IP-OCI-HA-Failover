Deploying BIG-IP VE in Oracle Cloud Infrastructure (OCI)
========================================================

To deploy BIG-IP Virtual Edition (VE) manually in the OCI, you must bring your own `image` and `license`, and then do the following:

- :ref:`1. Download a BIG-IP VE image <Oracle_download_step_1>`
- :ref:`2. Create an Oracle object storage bucket <Oracle_download_step_2>`
- :ref:`3. Create a custom image <Oracle_download_step_3>`
- :ref:`4. Create a compartment and define a Virtual Cloud Network (VCN) <Oracle_download_step_4>`
- :ref:`5. Deploy a BIG-IP VE instance <Oracle_download_step_5>`
- :ref:`6. Configure an HA cluster <Oracle_download_step_6>`



.. _Oracle_download_step_1:

Download a BIG-IP VE image
----------------------------

Get a BIG-IP VE image (.qcow2.zip file) onto a local drive, then upload this image to Oracle storage.

1. Open a browser, visit the `F5 Downloads page <https://downloads.f5.com>/>`_, and then log in or register.

2. On the Downloads Overview page, click :guilabel:`Find a Download`.

3. Under :guilabel:`Product Line`, click :guilabel:`BIG-IP <version>/Virtual Edition`, where <version> is the latest product line version offered or the version you want to download.

4. Under :guilabel:`Name`, click :guilabel:`xx.x.x.x_Virtual-Edition`, where xx.x.x.x is the product container you want to download, and then at the license agreement notification click :guilabel:`I Accept`.

5. Under :guilabel:`Filename`, click one of the ``.qcow2.zip`` image files.

6. Choose the download location closest to you.

7. When the file finishes downloading, unzip the ``.qcow2.zip`` file to a local drive. **Tip**: For Windows, use *7-Zip* or for Linux or Mac, use *unzip*.

8. Once unzipped, if necessary extract the ``.qcow2`` from the ``.tar`` file with ``tar xvfz <filename>.tar``, using *7-Zip*.



.. _Oracle_download_step_2:

Create an OCI object storage bucket and a pre-authenticated request
---------------------------------------------------------------------

Create an OCI object storage bucket, and then upload the ``.qcow2`` file.

1. Open the `Oracle OCI console <https://console.us-phoenix-1.oraclecloud.com/>`_ for the region in which you want to deploy (for example, ``PHX`` console).

2. At the :guilabel:`Cloud Tenant` prompt, enter your OCI tenant name, and then log in.

3. In the top-left corner, expand the menu icon, and then in the left pane under :guilabel:`Storage`, click :guilabel:`Object Storage`.

4. Expand the :guilabel:`Compartment` drop-down list, select a compartment, and then click :guilabel:`Create Bucket`.

5. In the :guilabel:`BUCKET NAME` box, enter a name, and then click :guilabel:`Create Bucket`.

6. In the center pane, find your bucket, hover your mouse over the :guilabel:`...`, and then on the pop-up menu select :guilabel:`Create Pre-Authenticated Request`.

7. In the :guilabel:`NAME` box, enter a name, and then in the :guilabel:`EXPIRATION DATE/TIME` box, select a date for expiration.

8. Leave all other settings with the default values, and then click :guilabel:`Create Pre-Authenticated Request`.

9. Under :guilabel:`Objects`, click :guilabel:`Upload Object`, browse for the .qcow2 file you downloaded in the previous procedure, and then click :guilabel:`Upload Object`.

10. The OCI Console provides the :guilabel:`PRE-AUTHENTICATED REQUEST URL`. Copy this URL for use in the next procedure_ .. _Oracle_download_step_3:



.. _Oracle_download_step_3:

Create a custom image
----------------------

Create a custom image to deploy the BIG-IP VE.

1. In the top-left corner expand the menu icon, and then under :guilabel:`Compute` click :guilabel:`Custom Images`.

2. Click :guilabel:`Import Image**`.

3. In the :guilabel:`NAME` box, enter a name.

4. Expand the :guilabel:`OPERATING SYSTEM` box, select :guilabel:`Linux`.

5. In the :guilabel:`OBJECT STORAGE URL` box, paste the URL for the Pre-Authenticated Request you copied in step 10 of the previous procedure_ .. _Oracle_download_step_2:.

6. Under :guilabel:`IMAGE TYPE` click the ``qcow2`` option.

7. Under :guilabel:`LAUNCH MODE` click the ``EMULATED MODE`` option.

8. Click :guilabel:`Import Image`.

The import process starts and can take several minutes. Once the import is complete and the custom image is ready to use, the tile next to the image name changes from orange to green.
**Tip**: Refresh the browser page frequently to display the status change.



.. _Oracle_download_step_4:

Create a compartment and define a Virtual Cloud Network (VCN)
-------------------------------------------------------------

Create a compartment to control access to your resources.

1. In the top-right corner expand the menu icon, and then under :guilabel:`Identity` click :guilabel:`Compartments`.

2. Click :guilabel:`Create Compartment`.

3. Enter a :guilabel:`Name` and a :guilabel:`Description`, and then click :guilabel:`Create Compartment`.

4. In the top-right corner expand the menu icon, and then under :guilabel:`Networking` click :guilabel:`Virtual Cloud Network (VCN)`.

5. In the left-side menu, under :guilabel:`List Scope`, expand the :guilabel:`Compartment` list, and select your compartment.

6. Click :guilabel:`Create Virtual Cloud Network`, enter a :guilabel:`Name`, select the :guilabel:`Create Virtual Cloud Network Only` option, and then
   define :guilabel:`CIDR Block`.

7. Accept the default values for the remaining settings, and then click :guilabel:`Create Virtual Cloud Network`.

8. Click your VCN in the list, and then click :guilabel:`Create Subnet`. You will repeat steps 8-10 for the following four subnets; one for management (public),
   one for internal traffic (private), one for external traffic (public), and one for high availability (public).

9. For each subnet, enter a :guilabel:`Name`, select a :guilabel:`Availability Domain` from the list, define a unique :guilabel:`CIDR Block`, select
   the default :guilabel:`Route Table` for the VCN, and then select either the :guilabel:`Private Subnet` or :guilabel:`Public Subnet` option accordingly (see previous step).

10. Click to enable the :guilabel:`USE DNS HOSTNAMES IN THIS SUBNET` option, select the default :guilabel:`DNS Label` and the default :guilabel:`DHCP Option`
    for your VNC, leave the default :guilabel:`Security Lists` as is, and then click :guilabel:`Create`.

11. To modify the security lists and allow TCP ports `80` and `443` inbound and specify which IP addresses are allowed to communicate with the
    management interface, in the left-side menu, under :guilabel:`Network`, click :guilabel:`Security Lists`, click :guilabel:`Edit all Rules`,
    under :guilabel:`Allow Rules for Ingress`, click :guilabel:`Add Rule`, define the :guilabel:`Source CIDR` as 0.0.0.0/0 and the :guilabel:`DESTINATION PORT RANGE` as 8443,
    and then click :guilabel:`Save Security List Rules`.


.. _Oracle_download_step_5:

Deploy a BIG-IP VE instance
------------------------------

Deploy a BIG-IP VE instance from the custom image created in the previous procedure.

1. In the top-right corner expand the menu icon, and then under :guilabel:`Compute` click :guilabel:`Instances`.

2. Click :guilabel:`Create Instance`.

3. In the :guilabel:`NAME` box, enter a name, and then expand the :guilabel:`AVAILABILITY DOMAIN` textbox and select a domain in which you want to deploy the BIG-IP.

4. Under :guilabel:`BOOT VOLUME` click the :guilabel:`CUSTOM IMAGE` option, and then  expand the :guilabel:`IMAGE` textbox, and select the image you created in the previous procedure_ .. _Oracle_download_step_3:.

5. Under the :guilabel:`BOOT VOLUME SIZE(IN GB)` section is the size of the volume for the image you uploaded. This value will change from BIG-IP TMOS version to version. If you want to create a larger initial boot volume,
   click the :guilabel:`CUSTOM BOOT VOLUME SIZE` checkbox, and then enter the volume size.

6. Under :guilabel:`SHAPE TYPE` click the :guilabel:`VIRTUAL MACHINE` option, and then expand the :guilabel:`SHAPE` list and select an appropriate shape based on your requirements.
   Shapes restrict the number of vCPUs, VNICs, and allocated memory. For more information about BIG-IP multi-NIC requirements, refer to the `BIG-IP VE Requirements <https://support.f5.com/csp/article/K14810>`_
   and the `Instance Shapes within OCI <https://docs.us-phoenix-1.oraclecloud.com/Content/Compute/Concepts/computeoverview.htm>`_ overview.

7. Under :guilabel:`Networking` expand the :guilabel:`SUBNET` list and select a subnet to which you want to attach the BIG-IP VE management interface.

8. If you want to access the BIG-IP directly from the Internet, then click the :guilabel:`ASSIGN PUBLIC IP ADDRESS` checkbox.

9. Click :guilabel:`Create Instance`. Once provisioning the instance is complete, the tile next to the instance name changes from orange to green.


.. _Oracle_download_step_6:

Configure an HA cluster
--------------------------

Create a High Availability (HA) device service cluster and configure fail-over objects to move to the active BIG-IP device within that cluster.

- Follow the recommendations for `Configuring BIG-IP device service clustering <https://support.f5.com/kb/en-us/products/big-ip_ltm/manuals/product/bigip-system-device-service-clustering-administration-13-1-0.html>`_.

- Create your fail-over objects for the OCI environment. Fail-over objects in F5 BIG-IP terms typically include, Floating Self-IP, SNATs Addresses, and Virtual Servers.
  See the `Managing failover using HA groups <https://support.f5.com/kb/en-us/products/big-ip_ltm/manuals/product/bigip-system-device-service-clustering-administration-13-1-0/6.html#GUID-97FC699F-75D6-42C0-916D-05E8CBFB71E7>`_
  topic for more information.

- In the OCI environment, public and private IP addresses are mapped to floating self-IP addresses and virtual servers.

- Create a new virtual server; for example, create a new private IP address under one of the secondary VNICs assigned to the BIG-IP instance in OCI.

1. In the top-left corner, expand the menu icon, under :guilabel:`Compute` click :guilabel:`Instances`, hover your mouse over the :guilabel:`...` next to the  BIG-IP Instance
   you created in the previous procedure_ .. _Oracle_download_step_4:, and then click :guilabel:`View Instance Details`.

2. In the left menu, under :guilabel:`Resources` click :guilabel:`Attached VNICs`.

3. Under :guilabel:`Attached VNIC` click the VNIC which corresponds with the network on which you want to create your fail-over IP Address,and then copy the `OCID` for use later in this procedure.

4. Under :guilabel:`IP Addresses`, click :guilabel:`Assign Private IP Address` to select an availability domain in which you want to deploy the BIG-IP.

5. In the :guilabel:`PRIVATE IP ADDRESS` text box, you can enter a preferred IP address or leave blank to have an available one auto-assigned.

6. In the :guilabel:`Public IP Address` pane, if you want to assign a public IP address to be mapped externally to the Internet, click the :guilabel:`RESERVED PUBLIC IP` option,
   and then expand :guilabel:`COMPARTMENT` and select the same compartment in which your BIG-IP is deployed.

7. Expand the :guilabel:`RESERVED PUBLIC IP` list, select a previously created :guilabel:`Reserved Public IP` or select :guilabel:`Create a New Reserved Public IP`, and then click :guilabel:`Assign`.

8. Once this `Private IP Address` is created, copy the `Private IP OCID` for use later in this procedure.
   You can now create the object (Floating Self-IP, SNAT, or Virtual Server) on the BIG-IP cluster and sync the configuration between the two.
   Once you create the object on the BIG-IP, customize the scripts to `move` the fail-over objects to the `Active` device in an HA cluster.

9. Download the following files from the `BIG-IP OCI HA Fail-over directory <https://github.com/snowblind-/BIG-IP-OCI-HA-Failover>`_.

.. code-block:: console

    active
    oci-curl
    vnicext2.json
    vnicint2.json

10. Copy these files to the `/config/failover` directory on both BIG-IP devices on which you previously set up a cluster.

11. In our `Virtual Server` example, configure it to attach to the `ext2` VNIC.

12. In the file `vnicext2`, replace the section with the example `ocid` with the `ocid` copied in step 3 of this procedure.

.. code-block:: json

    {
      "vnicId" : "ocid1.vnic.oc1.phx.abyhREPLACETHISWITHYOUROCIDShs5mzua7a"
    }

13. Edit the `active` file and replace the `iaas.us-phoenix-1.oraclecloud.com` with the appropriate OCI API endpoint for the `region` in which you are deployed, replace the `ocid` with the `Private IP OCID` you copied,

    when you created the Private IP Address in step 8, and then delete the second command as it is not necessary for this example.

14. Edit the `oci-curl` file and replace the `tenancyID`, `authUserId`, `keyFingerprint`, and `privateKeyPath` with your values.

15. Ensure that the `oci-curl` and `active` files are set to the `execute` (+x) permissions.

16. To test the fail-over, on the `Active` BIG-IP in the cluster, run the following command:

.. code-block:: console

    tmsh run sys failover standby
