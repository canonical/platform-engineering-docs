Service Health Guidance
=======================

Without a centralized dashboard, the :ref:`Ninja`
must rely on fragmented monitoring tools or manual processes, increasing the
risk of delayed responses to production issues. Implementing a centralized
Grafana dashboard (referred as "Platform Engineering dashboard") allows them to
quickly assess the health of all deployed products in one place.

Each charm will provide (where applicable) metrics for Latency, Traffic, Errors
and Saturation, following the principles of the `Four Golden Signals <https://sre.google/sre-book/monitoring-distributed-systems/#xref_monitoring_golden-signals>`_  of monitoring.

Refer to `ISD204 - Platform Engineering Services Health Dashboard <https://docs.google.com/document/d/1AO3HB-cZRS_oFWhZ2baA8lrU8oC_gWfIesP0Hkk3aGc/edit?usp=sharing>`_ for more details about it.

The `Platform Engineering dashboard <https://cos-ps6.is-devops.canonical.com/prod-cos-k8s-ps6-is-charms-grafana/d/d4a0ab60-1744-4be7-a16a-547fbff975ad/platform-engineering-services-health?orgId=1>`_ is available at PS6 only.
Access requires a VPN connection.

**Contents**

.. toctree::
   :maxdepth: 2

   Create Dashboard <create-dashboard>
   Add to the Platforms Engineering Dashboard <add-to-pe-dashboard>
