## Physical eSIM 
is a physical SIM card that can accept eSIMs offered by various providers.
There is something like this mentioned in X1 Nano G1 specs https://psref.lenovo.com/syspool/Sys/PDF/ThinkPad/ThinkPad_X1_Nano_Gen_1/ThinkPad_X1_Nano_Gen_1_Spec.PDF:

> WWAN  
>    Wireless WAN upgradable to 4G or 5G (with antenna ready)•  
>    Fibocom L850-GL, 4G LTE CAT9, M.2 card  
>    Qualcomm® Snapdragon™ X55 5G Modem-RF System, Sub-6 GHz, M.2 card, with embedded eSIM functionality  
>    No support•  
> SIM Card  
>    **Gemalto eSIM card (physical)**  
>    No SIM card inbox

In HP it seems that there is something similar https://h30434.www3.hp.com/t5/Notebook-Wireless-and-Networking/Intel-XMM-7360-LTE-Advanced-Softpaq-sp91807-aktiviert-eSIM/td-p/8571531:

> Most laptops with eSIM functionality have an actual chip-like eSIM soldered to the motherboard, thus providing eSIM capabilities to the laptop.  What HP does is slightly different - they provide a special, physical, programmable, removable eSIM chip from Thales / Gemalto , which comes installed in the regular removable SIM card slot.  You can typically only get that when you custom-order a laptop and specify that as an option."  


> Now: note that if you're on (or have ordered) a more recent laptop with a 5G module (HP uses the "Intel 5000 5G Solution WWAN module") that module has built-in eSIM capabilities - so you don't need the Gemalto eSIM card - the 5000 has eSIM on board.

I've encounteres similar solutions from two companies (more targeted at individual customers)
- https://esim.5ber.com/
- https://esim.me/

What's interesing they seem to charge for the ability to enroll a new eSIM configuration.

There was one interesing note on HP fourm about esim.me service:  

> "Now, the site claims that the cards are only compatible with Android devices, and that you must use their app to load profiles onto the card.  But actually the converse is true:  IF You are using their card on an Android device, it will work, and in that case you need the app.  However, you only need the app because Android does not (yet) understand eSIM cards natively.
> 
> But the esim.me cards turn out to be actual, valid, blank, multiprofile eSIM cards, and if you put them in a Windows 11 laptop with "4G" compatibility, Windows will recognize it - natively - as an eSIM card, it will default (in the US) to network "Telco Village" and Windows will let you add eSIM Profiles to it just as you'd expect.  The "Microsoft Plans" app also works as expected.