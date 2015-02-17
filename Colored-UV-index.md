If you want a colored UV index cell at the front page replace the following code in your index.html.tmpl


Replace 

~~~~~~
#if $day.UV.has_data
<tr>
<td class="stats_label">UV</td>
<td class="stats_data">$current.UV</td>
</tr>
#end if
~~~~~~

with:

~~~~~~
#if $day.UV.has_data
  <tr>
  <td class="stats_label">UV</td>
  #if $current.UV.raw <= 2.4
    <td class="stats_data" style="background-color: limegreen">$current.UV</td>
  #else if $current.UV.raw >= 2.5 and $current.UV.raw <= 5.4
    <td class="stats_data" style="background-color: yellow">$current.UV</td>
  #else if $current.UV.raw >= 5.5 and $current.UV.raw <= 7.4
    <td class="stats_data" style="background-color: orange">$current.UV</td>
  #else if $current.UV.raw >= 7.5 and $current.UV.raw <= 10.4
    <td class="stats_data" style="background-color: red">$current.UV</td>
  #else if $current.UV.raw > 10.5
    <td class="stats_data" style="background-color: violet">$current.UV</td>
  #end if
  </tr>
#end if
~~~~~~

Suggestion: Change the font colour to black so it is legible against the colours.


To add a colored UV-Index number to the smartphone template edit the index.html.tmpl inside your smartphone folder.

After the line

~~~~~~
	<li><a href="wind.html">Wind: $current.windSpeed from $current.windDir</a></li>
~~~~~~

add

~~~~~~
#if $day.UV.has_data
  #if $current.UV.raw <= 2.4
    <li><a href="uv.html">UV-Index: <span style="color: limegreen">$current.UV</span></a></li>
  #else if $current.UV.raw >= 2.5 and $current.UV.raw <= 5.4
    <li><a href="uv.html">UV-Index: <span style="color: yellow">$current.UV</span></a></li>
  #else if $current.UV.raw >= 5.5 and $current.UV.raw <= 7.4
    <li><a href="uv.html">UV-Index: <span style="color: orange">$current.UV</span></a></li>
  #else if $current.UV.raw >= 7.5 and $current.UV.raw <= 10.4
    <li><a href="uv.html">UV-Index: <span style="color: red">$current.UV</span></a></li>
  #else if $current.UV.raw > 10.5
    <li><a href="uv.html">UV-Index: <span style="color: darkviolet">$current.UV</span></a></li>
  #end if
#end if
~~~~~~

then you will need a smartphone/uv.html.tmpl

~~~~~~~
<!DOCTYPE html>
<head>
  <meta charset="utf-8" />
  <title>UV Index in $station.location</title>
  <link rel="stylesheet" href="http://code.jquery.com/mobile/1.1.0/jquery.mobile-1.1.0.min.css" />
  <script src="http://code.jquery.com/jquery-1.7.1.min.js"></script>
  <script type="text/javascript" src="http://code.jquery.com/mobile/1.1.0/jquery.mobile-1.1.0.min.js"></script>
  <meta name="viewport" content="width=device-width, initial-scale=1">
</head>
<body>
  <div data-role="page" data-add-back-btn="true" data-theme="a">
    <div data-role="header">
      <h1>UV Index</h1>
   </div>
   <div data-role="content">
     <h4>UV Index for day</h4>
     <img src="../dayuv.png"/>
     <ul data-role="listview" data-inset="true" data-divider-theme="b">
       <li data-role="list-divider">Today's data</li>
       <li>Max UV Index: $day.UV.max at $day.UV.maxtime</li>
     </ul>

     <h4>UV Index last 30 days</h4>
     <img src="../monthuv.png" />
    </div>
    <div data-role="footer">
      <h4>weewx v $station.version</h4>
    </div>
  </div>
</body>
~~~~~~~
and add this to the [CheetahGenerator] section of your skin.conf

~~~~~~~
[[[MobileUV]]]
   template = smartphone/uv.html.tmpl

~~~~~~~~


More information about the UV-Index:
http://en.wikipedia.org/wiki/Ultraviolet_index