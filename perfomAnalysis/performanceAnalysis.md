### Ping Analysis

 Are RTT and geographic distance correlated positively, negatively, or not at all? If applicable, also comment on the strength of correlation (weak vs. strong).

    According to the plot, there is a positive correlation. The correlation between the average RTT and distance is relatively strong even though there are outliers.

    Following this positive correlation, the farthest distance of 7208 miles has the longest avg RTT and the shortest distance of 151 miles has the least amount of hops.


2. Why do you think you observe this trend (or lack thereof)?
    
    This trend could occur due to the number of routers between a connection increasing with distance, though this doesn't always increase the RTT as fewer intermediate routers don't necessarily mean quicker packet transfer.


    The RTT could be increasing due to weakening connections caused by the distance between some of the routers, if the routers in question are wireless.


    Another issue could be intermediate routers over a long physical distance might manage more network connections than two routers closer together and therefore this increase in network traffic could cause this positive correlation.


### Traceroute Analysis


1. Are # hops and geographic distance correlated positively, negatively, or not at all? If applicable, also comment on the strength of the correlation (weak vs. strong).


    According to the plot, there is a weak negative correlation between the number of hops and the geographic distance.


2. Why do you think you observe this trend (or lack thereof)?
   
    As router technology improves, distance has little effect on packet travel.
    The number of hops it takes to get to a domain is partly dependent of the network connection of routers and their algorithms. Each connection has its own path through intermediate routers and in some cases despite being farther away in physical distance, a connection can have a more efficient route for getting a packet to its destination due to these intermediate routers' connection to other intermediate routers or network traffic.


    So despite there being a minor negative correlation, there is almost no effect and the negative correlation could be due to significant outliers.
