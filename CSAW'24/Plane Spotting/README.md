**Title**: Plane Spotting  
**Category**: OSINT

---

## **Problem Statement**

My friend swears he saw a Russian plane in the US this June, but I don't believe him. He also says he saw it was parked next to a plane owned by an infamous former president! Can you find the registration number of this Russian plane, the FAA airport code of where the plane was spotted parked next to the other, as well as the registration number of the plane owned by that president?

My friend also tells me that a few days earlier, ANOTHER Russian plane flew to the US. Find the city that Russian plane was closest to at 21:07:40 Z during its flight to the US!

**Flag format**: `csawctf{RegistrationNumberRUS_AirportCode_RegistrationNumberUS_CityName}`

**Note**: Do not include any special characters (dashes, etc.)

`author: lpv2013`

---

## **Solution**

I began by conducting a quick Google search with the query "russian plane parked in the us june 2024" This led me to several articles discussing about involvement of a Russian plane in the US. So yes, I was on a right track. Among these, I found this [article](https://www.radaratlas.com/trump-kremlin-airplane-together-in-washington-dc-after-the-debate/) that provided me with most of the necessary information.

- RegistrationNumberRUS: RA96018
- AirportCode: IAD
- RegistrationNumberUS: N757AF

![Planes in Washington D.C.](https://i.imgur.com/GPzZxQ4.png)

To find information about the earlier Russian flight, I found this [article](https://en.as.com/latest_news/russian-plane-is-headed-to-us-airspace-what-is-its-direction-and-what-kind-of-aircraft-is-n/) discussing the flight of the plane with Registration Number `RSD738`. I used [FlightAware](https://www.flightaware.com/live/flight/RSD738/history/20240626/2030Z/KJFK/KIAD/tracklog) to view the tracklog of this flight, focusing on the timestamp 21:07:40 Z, which converts to 5:07 PM EDT. 

![Flight Path of RSD738](https://i.imgur.com/22KlLXy.png)

I found that at 05:07:25 PM, the plane was located at coordinates 40°13'17.0"N 74°43'03.0"W, which is closest to Trenton, New Jersey, US.

- CityName: Trenton

With all the necessary information collected, our final flag is `csawctf{RA96018_IAD_N757AF_Trenton}`
