:tocdepth: 3

policy/files/unified2/main.zeek
===============================
.. zeek:namespace:: Unified2


:Namespace: Unified2
:Imports: :doc:`base/utils/dir.zeek </scripts/base/utils/dir.zeek>`, :doc:`base/utils/paths.zeek </scripts/base/utils/paths.zeek>`

Summary
~~~~~~~
Redefinable Options
###################
=================================================================================== =====================================================================
:zeek:id:`Unified2::classification_config`: :zeek:type:`string` :zeek:attr:`&redef` The classification.config file you would like to use for your alerts.
:zeek:id:`Unified2::gen_msg`: :zeek:type:`string` :zeek:attr:`&redef`               The gen-msg.map file you would like to use for your alerts.
:zeek:id:`Unified2::sid_msg`: :zeek:type:`string` :zeek:attr:`&redef`               The sid-msg.map file you would like to use for your alerts.
:zeek:id:`Unified2::watch_dir`: :zeek:type:`string` :zeek:attr:`&redef`             Directory to watch for Unified2 records.
:zeek:id:`Unified2::watch_file`: :zeek:type:`string` :zeek:attr:`&redef`            File to watch for Unified2 files.
=================================================================================== =====================================================================

Types
#####
====================================================================== =
:zeek:type:`Unified2::Info`: :zeek:type:`record` :zeek:attr:`&log`     
:zeek:type:`Unified2::PacketID`: :zeek:type:`record` :zeek:attr:`&log` 
====================================================================== =

Redefinitions
#############
============================================================= ===========================================================================================================================================================================================================
:zeek:type:`Log::ID`: :zeek:type:`enum`                       
                                                              
                                                              * :zeek:enum:`Unified2::LOG`
:zeek:type:`fa_file`: :zeek:type:`record` :zeek:attr:`&redef` 
                                                              
                                                              :New Fields: :zeek:type:`fa_file`
                                                              
                                                                u2_events: :zeek:type:`table` [:zeek:type:`count`] of :zeek:type:`Unified2::IDSEvent` :zeek:attr:`&optional` :zeek:attr:`&create_expire` = ``5.0 secs`` :zeek:attr:`&expire_func` = :zeek:type:`function`
                                                                  Recently received IDS events.
============================================================= ===========================================================================================================================================================================================================

Events
######
===================================================== ===================================================
:zeek:id:`Unified2::alert`: :zeek:type:`event`        Reconstructed "alert" which combines related events
                                                      and packets.
:zeek:id:`Unified2::log_unified2`: :zeek:type:`event` The event for accessing logged records.
===================================================== ===================================================

Hooks
#####
============================================================= =
:zeek:id:`Unified2::log_policy`: :zeek:type:`Log::PolicyHook` 
============================================================= =


Detailed Interface
~~~~~~~~~~~~~~~~~~
Redefinable Options
###################
.. zeek:id:: Unified2::classification_config
   :source-code: policy/files/unified2/main.zeek 24 24

   :Type: :zeek:type:`string`
   :Attributes: :zeek:attr:`&redef`
   :Default: ``""``

   The classification.config file you would like to use for your alerts.

.. zeek:id:: Unified2::gen_msg
   :source-code: policy/files/unified2/main.zeek 21 21

   :Type: :zeek:type:`string`
   :Attributes: :zeek:attr:`&redef`
   :Default: ``""``

   The gen-msg.map file you would like to use for your alerts.

.. zeek:id:: Unified2::sid_msg
   :source-code: policy/files/unified2/main.zeek 18 18

   :Type: :zeek:type:`string`
   :Attributes: :zeek:attr:`&redef`
   :Default: ``""``

   The sid-msg.map file you would like to use for your alerts.

.. zeek:id:: Unified2::watch_dir
   :source-code: policy/files/unified2/main.zeek 15 15

   :Type: :zeek:type:`string`
   :Attributes: :zeek:attr:`&redef`
   :Default: ``""``

   Directory to watch for Unified2 records.

.. zeek:id:: Unified2::watch_file
   :source-code: policy/files/unified2/main.zeek 12 12

   :Type: :zeek:type:`string`
   :Attributes: :zeek:attr:`&redef`
   :Default: ``""``

   File to watch for Unified2 files.

Types
#####
.. zeek:type:: Unified2::Info
   :source-code: policy/files/unified2/main.zeek 37 64

   :Type: :zeek:type:`record`

      ts: :zeek:type:`time` :zeek:attr:`&log`
         Timestamp attached to the alert.

      id: :zeek:type:`Unified2::PacketID` :zeek:attr:`&log`
         Addresses and ports for the connection.

      sensor_id: :zeek:type:`count` :zeek:attr:`&log`
         Sensor that originated this event.

      signature_id: :zeek:type:`count` :zeek:attr:`&log`
         Sig id for this generator.

      signature: :zeek:type:`string` :zeek:attr:`&optional` :zeek:attr:`&log`
         A string representation of the *signature_id* field if a sid_msg.map file was loaded.

      generator_id: :zeek:type:`count` :zeek:attr:`&log`
         Which generator generated the alert?

      generator: :zeek:type:`string` :zeek:attr:`&optional` :zeek:attr:`&log`
         A string representation of the *generator_id* field if a gen_msg.map file was loaded.

      signature_revision: :zeek:type:`count` :zeek:attr:`&log`
         Sig revision for this id.

      classification_id: :zeek:type:`count` :zeek:attr:`&log`
         Event classification.

      classification: :zeek:type:`string` :zeek:attr:`&optional` :zeek:attr:`&log`
         Descriptive classification string.

      priority_id: :zeek:type:`count` :zeek:attr:`&log`
         Event priority.

      event_id: :zeek:type:`count` :zeek:attr:`&log`
         Event ID.

      packet: :zeek:type:`string` :zeek:attr:`&optional` :zeek:attr:`&log`
         Some of the packet data.
   :Attributes: :zeek:attr:`&log`


.. zeek:type:: Unified2::PacketID
   :source-code: policy/files/unified2/main.zeek 30 35

   :Type: :zeek:type:`record`

      src_ip: :zeek:type:`addr` :zeek:attr:`&log`

      src_p: :zeek:type:`port` :zeek:attr:`&log`

      dst_ip: :zeek:type:`addr` :zeek:attr:`&log`

      dst_p: :zeek:type:`port` :zeek:attr:`&log`
   :Attributes: :zeek:attr:`&log`


Events
######
.. zeek:id:: Unified2::alert
   :source-code: policy/files/unified2/main.zeek 280 285

   :Type: :zeek:type:`event` (f: :zeek:type:`fa_file`, ev: :zeek:type:`Unified2::IDSEvent`, pkt: :zeek:type:`Unified2::Packet`)

   Reconstructed "alert" which combines related events
   and packets.

.. zeek:id:: Unified2::log_unified2
   :source-code: policy/files/unified2/main.zeek 67 67

   :Type: :zeek:type:`event` (rec: :zeek:type:`Unified2::Info`)

   The event for accessing logged records.

Hooks
#####
.. zeek:id:: Unified2::log_policy
   :source-code: policy/files/unified2/main.zeek 9 9

   :Type: :zeek:type:`Log::PolicyHook`



