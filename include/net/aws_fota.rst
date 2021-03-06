.. _lib_aws_fota:

AWS FOTA
########

This library combines the :ref:`lib_aws_jobs` and :ref:`lib_fota_download` libraries to create a user-friendly library that can perform firmware-over-the-air (FOTA) update using HTTP and MQTT TLS.

It connects to the specified broker using the existing or given certificates and uses a `TLS <https://www.ietf.org/rfc/rfc5246.txt>`_
connection for the MQTT connection.
This means that the data sent in each MQTT message is encrypted.

Note that a device connected to the AWS MQTT broker with a valid but different certificate, which has the same security attributes in AWS as the other device, and which is able to subscribe to the same MQTT topic as the other device, will also receive the message sent to that topic.

MQTT is used to receive notification that an update is available, and to retrieve metadata about the update.
HTTP is used to download the update payload.

It is up to the application that uses the library to restart when the FOTA is complete.

Configuration
*************

Use Kconfig to configure the MQTT payload buffer sizes and the buffers used to store the version string, host name, and file path.


Implementation
**************

The implementation uses the job document shown below for passing information from AWS Jobs to the device:

.. code-block:: javascript

   {
     "operation": "app_fw_update",
     "fwversion": "v1.0.2",
     "size": 181124,
     "location": {
       "protocol": "http:",
       "host": "s3.amazonaws.com",
       "path": "/nordic-firmware-files/0943dfbf-cb10-4eb7-8277-a8b179eaf4ff"
      }
   }

The current implementation only uses information from `host` and `path` in this document.

The following sequence diagram shows how a FOTA is implemented through the use of `AWS IoT Jobs <https://docs.aws.amazon.com/iot/latest/developerguide/iot-jobs.html>`_, `AWS IoT MQTT <https://docs.aws.amazon.com/iot/latest/developerguide/mqtt.html>`_, and `AWS S3 <https://docs.aws.amazon.com/s3/index.html>`_ in this library.

.. figure:: ../../doc/nrf/images/aws_fota_dfu_sequence.svg
   :alt: AWS FOTA sequence diagram for doing FOTA through AWS Jobs

Limitations
***********

* Currently, the library only uses HTTP for downloading the firmware.
  It is, however, possible to have it work with HTTPS - see :ref:`lib_download_client`.
  These changes need to be applied to :ref:`lib_fota_download` to enable downloading firmware through HTTPS.
* The library requires content-range header to be present in the HTTP response from the server.
  This limitation is inherited from :ref:`lib_download_client` which is used by :ref:`lib_fota_download`.

API documentation
*****************

| Header file: :file:`include/net/aws_fota.h`
| Source files: :file:`subsys/net/lib/aws_fota/`

.. doxygengroup:: aws_fota
   :project: nrf
   :members:
