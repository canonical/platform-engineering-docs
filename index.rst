Platform Engineering Team
=========================

The Platform Engineering team (formerly IS DevOps) is dedicated to building, transitioning, and maintaining operations-specific charms
while adhering to the latest charming standards. Our goal is to support both internal and external ITOps teams in adopting a fully 
model-driven operations framework, maximizing its benefits.

Our responsibilities include:

-  Designing and implementing world-class charmed solutions for application and infrastructure management that serve both internal teams and external organizations.
-  Ensuring seamless integration with the latest charmed solutions from product engineering.
-  Collaborating with IS to deploy these solutions, gather feedback, and refine them for optimal functionality.
-  Maintaining and regularly updating the charms we own.

For a full list of charms we are responsible for, see the `Charm Engineering Releases <https://releases.juju.is/?team=IS>`_ page.


Our stakeholders
----------------

#. IS (Canonical ITOps): Canonical’s IS team oversees a wide range of responsibilities, from data center management and Juju infrastructure provisioning 
   to application management and security. As the largest stakeholder in our work, IS plays a key role in shaping our roadmap, ensuring that our services
   are thoroughly tested internally before being deployed in customer environments.
   The majority of functional requirements for our charms come from IS, forming the foundation of the products we develop. However, it's crucial to 
   maintain a broader perspective, ensuring that our solutions follow industry best practices rather than ad-hoc fixes. Additionally, these solutions 
   must be designed to work seamlessly for external organizations, not just for our internal needs.

#. Security: Canonical Security team is responsible for ensuring compliance, security and risk management of our internal solutions. In recent cycles, we have 
   worked closely with the security team to develop charms for solutions like OpenCTI, Cloudflare, and Wazuh. 
   These will enable us to monitor our infrastructure, network, endpoints, and potential threats more effectively in the future.

#. Web: The Canonical Web team is responsible for developing our websites and front-end interfaces for various tools. 
   We have closely collaborated with them on the 12-factor project to streamline site deployment and management, leveraging the power of Juju and charms
   for long-term efficiency and scalability.

#. Community: The Community team focuses on fostering OSS community engagement and maintaining developer relations for various products.
   We frequently collaborate with them, as they rely on our support for developing solutions like Indico and Matrix to drive community involvement.

#. Others: Given our role as a bridge between product engineering and operations, we regularly collaborate with other charm engineering teams—
   such as Starcraft, Server, Launchpad, and Landscape—to build and refine solutions together.

Roadmap
-----------

* `Public Roadmap <https://docs.google.com/spreadsheets/d/1ykieRvaUVY4fS4ZqGH_jjyQuI6K-wBxgzQoTT2g_3vg/edit?gid=1941638273#gid=1941638273>`_

* `Team internal cycle planning and roadmap document <https://docs.google.com/spreadsheets/d/1iSkut6Qf_mm7_HynYeCX_lB47noCGJuXNe1ODrbXTPk/edit#gid=0>`_

Team members
------------
This is a global team with a team in each of the regions (EMEA, APAC and Americas). A full
list of everyone can be found on the `directory <https://directory.canonical.com/products/Platform%20Engineering>`_!


Resources
---------

* `Code of conduct <https://ubuntu.com/community/code-of-conduct>`_
* `Get support <https://discourse.charmhub.io/>`_
* `Private Mattermost channel <https://chat.canonical.com/canonical/channels/is-charms>`_
* `Contributing guide <https://github.com/canonical/is-charms-contributing-guide>`_



.. toctree::
   :hidden:
   :maxdepth: 3

   Platform Engineering <self>
   Delivery Workflows <delivery-workflows/index>
   Delivery Practices <delivery-practices/index>
   Engineering Practices <engineering-practices/index>
   Onboarding <onboarding/index>
   Reference <reference/index>
   Working with other teams <working-with-other-teams/index>
   Hiring <hiring/index>
   How to <how-to/index>
