# The Tags 

Wildlife Computers has sort of a confusing naming scheme for their tags and I don't completely understand. The two key things to know are there are the guts of the tag (circuit board / sensors) and they came come in a variety of shapes, which is just the housing but makes the names confusing. Wildlife Computers folks seem to refer to these as shapes so I've used that here.

## Spot

Most of the spots were deployed pre-BRS and so I don't have much to say about them. They are primarily Argos location tags. They are programmed differently (through the tag agent) than the SPLASH10 tags (see below). They are deployed in a similar manner and the data is retrieved similarly.

## SPLASH10

SPLASH10 tags are the majority of the tags that have been deployed during BRS. These tags have the ability to generate some depth and dive profile-like data. Some folks also refer to these as MK10 tags. I think this refers to the circuit board. Most (if not all) of ours have been SPLASH10-292. The 292 refers to the shape, but I don't know what it means specifically. This shape is designed for remote deployment with the LIMPET (Low Impact Minimally Percutaneous Electronic Transmitter) configuration [@andrews2008].

These tags are programmed with MK10 host.

Wildlife Computers claims that this shape is "good" to 2000 meters. I'm not sure exactly what "good" means but they do test these things in a pressure chamber. There do seem to be at least two configurations for the pressure transducer though. 

  - standard: -40 to 1000 meters at 0.5 meter resolution. Overpressure rating on the transducer is 2000 meters. Though note that the reading pins^[By pins I mean as the pressure increases the sensor continues to read some arbitrary max value.] at around 1700 meters.
  - extended depth: -80 to 2000 meters at 1.0 meter resolution. Overpressure rating on the transducer is 3000 meters.

Wildlife Computers technical staff reported that transducers are temperature calibrated to about 1% of full scale though the manufacture spec is tighter. Wildlife Computers has not been willing to give us the manufacturer or any detailed specs on the pressure transducers.

Greg Schorr put at least one SPLASH10 tag in a pressure chamber up to 3000 meters and found a max error of +/- 2.5% beyond the Wildlife Computers calibration [@schorr2014].

In addition to these configurations, Wildlife Computers technical staff has indicated that there was a design flaw in mounting pressure sensors until recently (at least 2017?) which is to be corrected (already is corrected?) and will reduce failures (§ \@ref(failures)).

## Fastloc (SPLASH10-F)

We've deployed a limited number of SPLASH10-F tags, which basically have everything that SPLASH10 have plus the addition of a fastloc board and antenna. I think the shape on these is 333, but I'm not positive. These tags are a little bigger and heavier than the SPLASH10 but are still deployed in a similar fashion using the LIMPET configuration.

These tags are also programmed with MK10 host.

