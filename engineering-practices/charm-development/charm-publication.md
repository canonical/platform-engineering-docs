# Charm publication


[ISD-151](https://docs.google.com/document/d/1wkucY_V7othjizqGoJcF8apF6WIhRPnI2OMM56mPzZc/edit?tab=t.0) defines the guidance for creating tracks on Charmhub.

Though this might differ for certain cases, for the large part we should follow the same practices.

Below is the summary of guidance, and more details can be found in the approved spec:

1.  Workloadless charms will be initially published to track “1”, which will increase whenever a breaking change is added.

2.  Workload charms will be published to a track matching the charmed application stable version, regardless of the versioning schema, preferring LTS versions. 
   For instance, Indico 3.3.2 will be deployed to the branches 3/[risk].

3.  Workload charms will be backwards compatible within the same track. 
   Breaking changes should be postponed until the new track is released as a consequence of the increased major workload version
   
4.  The default track will be the latest stable version. For instance, 2/stable when 1/edge, 1/stable, 2/edge and 2/stable are available. 
   This will mean that an operator will have to manually switch tracks in order to upgrade a charm to a higher track.
   