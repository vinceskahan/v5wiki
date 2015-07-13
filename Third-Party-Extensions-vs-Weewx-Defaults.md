###OBSOLETE. 
Well-behaved extensions, including `weewx-wd`, now use their own database
___
** Third Party Extensions and skins versus weewx standard schema and templates **

The default weewx schema includes many data fields, such as `extraTemp1`, `extraTemp2`, etc., that are only rarely used. Unfortunately, some third party extensions have leveraged this fact by using them for their own needs. This can trip up the unwary user.

The weewx default templates in `skins/Standard` conditionally display information if there is data in the database for certain fields (`extraTemp1`, `UV`, `radiation`, `rxCheckPercent`) or if `skin.conf` contains certain user-defined Extras variables (`radar_url`, `googleAnalyticsId`).   This can cause unexpected behavior with third-party extensions that use the same data fields or variables.

For example, the weewx-wd extension by default writes calculated data to `extraTemp`1 and `extraTemp2`, which causes the default weewx templates to report 'Pond Temperature' due to the presence of data in the `extraTemp1` field.

In those cases there are a few options for ensuring the extensions play nicely with weewx:
<ul>
<li> Reconfigure the extension to not use those fields or variables. There are a number of even more rarely used fields in the database that are not used in the typical templates (for example, `leafTemp2` or `soilMoist3`);</li>
<li> or comment out the block in the skin;</li>
<li> or wrapper the block in the skin to look for an `Extras` variable</li>
</ul>

~~~~~~~
    #if $Extras.has_key('sensor_extraTemp1_present')
    ...the default block goes here...
    #end if
~~~~~~~

Third party extensions which intercept the LOOP data coming into the weewx engine, such as weewx-wd, might make debugging unexpected information in the database more difficult.  In that case pay particular attention to your service_list in the toplevel `weewx.conf` and be certain you are running the desired add-on service(s).

To debug your LOOP data, run weewx in command line mode. Simplest way to do this is to stop your weewx daemon and run the process interactively via:

~~~~~~~~
    cd /home/weewx
    bin/weewxd/weewxd weewx.conf
~~~~~~~~