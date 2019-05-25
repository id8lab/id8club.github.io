---
layout: default
title: IoT Image Recognition
image: assets/images/jcucairns.jpg
comments: true
author: robert
published: true
permalink: IOT-Image-recognition.html
---

---

### Github repositories

- https://github.com/mvpclub/IOT-Image-recognition/
- https://github.com/92coorob/Facerec2
- https://github.com/92coorob/AttendanceApp


---

---

### IoT Image Recognition
This project was inspired by the poor attendance system currently implemented in JCU. How many times have you missed attendance because you forgot to tap in during class? for your average student it's probably more than you can count.

Wouldn't it be nice if you could just walk in to the classroom and your presence alone would be enough to clock your attendance.
This might be possible using face recognition. By having cameras scanning the faces of people in the classroom the attendance would all be automated.

It could potentially also stop people from being able to "tap and leave" by monitoring the presence of students throughout the entire lecture.


---

### Goals

- Make it easier for students and lecturers to get their attendance
- More accurate attendance taking (make sure students are actually in class and don't just tap and leave)


---


---

### Demo
<br>
the facerec main code has three different modes
  
  -main.py --mode input
  
  this mode is used for training new faces. you will have to add a user ID and then scan the new face for about 10<br> 
  seconds.
  <br>
  <br>
  This is the part where you have to add the ID
   <img src="https://lh3.googleusercontent.com/VLh3qF9ZyQkGXGse-9Shc1It49DAWqWO2vLR94Yph34kjJGEX0fcEBe1DvOOWKnWOP4qg-vBA0n-ybE0j-Dn6eqlAU2mrjGCO2BqlwkUc3_4Y5ucUclO7fNk35cQ8GRU_h_VJ6HkA1WwZY2cbxn8GpzzYDm_ukFI18yu9QKAJCbtdtiY-CjZyqR_ZAbFv8AV4EiFIw8prOQFSIZUcIHxMhpC0VexO2MZLgossfGO2gGl5wmc89JJIXkbWQswYKNj7CswkSUBnqyr8CcI5l9Hposj9m7w5Vze0Dtby4hEWYyRUv0VIZ8IPLFXzSyyiOJvIwwAeAE6LzXRxNiyOdyL5G55ZTG9_qysfwYllvMqybawGmm5tHTPT1ef_l-bC3Kad8KUEum2ksQCZC5XErQ92iUv6hxzRsxPl51e0AybKyi9wtZv9_LV3uKCpXMuV1Yaoafuj-YdNqObS4aKWC7HiXXQRbv8_qP5s5xaO2RKq_IhjBcltKGG2NAW3tPNb-7QiEkkM-hGvxwB63lUsvvORuXP_LaYaPx8nR52BSoJmmd653eMRaVSbKBlX_cUEbNda7LQ_sVZiAvsp7NmDc1oJIsj4zvQOmbFpnzt0h80b-dPa1B9U4rGrdjJnsD19oh_KWsztbLCIhNG7DN5H8ko8uRp=w979-h446-no">
   <br>
  <br>
  this is the scanning part
      <img src="https://lh3.googleusercontent.com/RBPqs7Dp51NvO4HHT4FlSGJ6RrTbsj7qI9XPUes98iKwf9uiaDyfUxbXZNSpAWlm1ZzGLv2vWIw698nDESb6p6I0Fxf9mMvpRAIFlF0KER1HkYtIgYEUgS4cWpP7_wMlWuurDr_EfAIv5VwVe2mrCuwtwMuIznP6RlkWvacBUD1XJrTEksN6ma6C2i4Lv63ZZJTuVZZ93e8vrDIaqzEkZfTrIPRTM-nPeG_ZQpCGk5cB3h1FQJyXU1izlakTfSpYRRszmBL-O4zoQeUcRB_u_7DvbHIWJbIg38HdPCALqk5xD7a-kZcH81uunBcJhxjBwlp4xPNHe2W4IvPefPFFB74r8ogGlTcw7glCEV90fdZXEX47qeyDsW9C_UeTVU7etTC9a-sm3HlMh0s4e8fa7Y_07mMqd8wrBXuK0FgV1lY__WX2PHBITD6jHbMP7L3RV1xsxCe3vuUeUlAVJvpdVMjAPcLFhknhR-x4QRbVrKZSUjooBVXTNhKg7PA0ueIATYuiJm-d3vEbQ2yqaiTLlEKnIZUKJsOMIUToee6ARqYCfPKWj6Sh__eu4kr57yvl-GEjLYuxmWLLxy_viYv8sgVCO-vykJMnc0v0TOuufK9bSEZDh1aQKOJ7hLcQMyKOjKBSymIp0Pi1DatxLDHRS5gH=w1375-h706-no">
      

  
  -main.py
  
   once you have scanned a face you can go ahead and recognice it by running main.py without any mode:
       <img src="https://lh3.googleusercontent.com/PL_xzj5PHO9pgjoafZ3uM2NnYY_KVny5TGSZL4LrWJ9RN_THB54ZdntZ_mAmR9RTWxlk-85W1KBWcWwNqJgdnzV6d8ASFSF_YfYQmwd7yZzWX3_orE2f0mcjeLPnoyhqddwWgQeH-SjrYlPKsOTfbEXOeHA2D_nQtAEe79HuF5EmTVvZ-jDZXC7-xpSF2PKs1nmx_ywznyywzHXmSX-NRkt7daWNF3Er_yUiTpKjQ55tyFnRN2Mrm1iG-B-O5vVOYevGzwvvhkJ0seqAlphK9_E5sFAB_Ayw1xBveLzMrN2pLYh_AA2DXiu9mNCuJKhM0jW5QDTFRkPNs0d9oDPIaryi9I3b8XbT5GBvndrxMI9_-Uj2XQCqjUlG1M5M5q_0wrSypTvjTeFnlU7R1Ri46YvEZGGa4tszphmr_GCTb5oYlYi0ddLpY0OnkU6DgwlrOJ9n_NVH9mKJdvBXYzSGHAvIFurHcBLz_TzOyEgBny4e2oXdvpdpmBPAuo6V6AZ1B9Srm5GYLzzZIttydDHrXK_I5iLlmKMYIK6G0tlBucSBcE_7od5FkvCo8_BZt7-Hp6u2GGlNOEz-iKwhfB4BeghHX3BdBTwkPQSMc3xX2iTERJTAR5J0DqW0bWCGU-p9e3CGZk1Kdz6NzsODTnX1MxLP=w1718-h966-no">
       
   
  -main.py --mode delete
  if you wish to delete a face from the database you can run the delete mode.
         <img src="https://lh3.googleusercontent.com/1GGU2IQ8BfRTy99_rs20R7d_b2gbPYFnzxXXqeF4KObJAynaSTpEq9KCX3aiXbHndxSlzYtDuec-Uji-K0lnLa5dcfB335csw59oQZL5OFyC96CEpE9tbo_uy2cKU6dOZzKqRH8OxSzCdopBO61d-4o1xYqQcBA79nPLFWx4fMYP70UeMUgfnJYVlv1GZKxb6THUfOmsJWnTAzhN9MrXVhRJ3RRb-Vohuff2yJj_3fb092RpBj9QkRcRdeDt8MWg_X1yrmXo5NFdqTMIy9u7BBYmOVLfPcklljJtno9OBKl0wswNFfI4NLsSr5PlvbS1UG_F56Pd1j5smuVuNFIpOBzwmmkI3NdbKtXuidb6lU494ym2kCIWIZogte39y-bOM0BpCOFgsrzBUOr1mrXA6t8ctU3FZcE78rh1ZRG0VCLh5Dw5mJ1IKPB4Iq14g8U4VQkqadqmUSUrzf3MVsoj6XuTbKqIpgqsOiGbTaE08SKY36jAdLxpCXZNLoxXEqUcYwhgCVY0DvgO_mAMQ0guMA0VK5OpEqg0dHt3RBEs4BNh55ntbriAskaeGxwpp5-f-0fSAemWYiosRJ_TQPsei3VoSj8lqgoQ5kXKt5TtT7h0jkYIS6FjvhjpwLMIFFTpWK_I182OhRhZioOK4juA63G4=w428-h147-no">



---

---

### The Attendance app

login page
         <img src="https://lh3.googleusercontent.com/1cggzTHJRXbCQT8Wz0wy-bNlpO427THBIlIHsJpPsjKLAkKQ6Lh9QDm9RznX7BsZTs_skcv-sshJDXEOyFWUWzV1yix7bFQRjVIO1AlZqBVyUwRqdDbvtmVAjEWmHc-pnwD79dgSrhN-QNCETUV2uocBUYHJJ8Ivevzhj8wuUoZUBCN7y5dLEM4Q5GDiaB9p6SFrdAY5iGquB_m62JS-2GUDGqcP1iYjKnZOx9Whl8C6MHwK1jjuu_QPbOEoDJxbidoVJ72F6hX0i5-IweoqRiqJzUz4jnTD394AaNvDMHX3lPMl_hiAZ4oEv757j5-qdRTwER5vuSYprD1MPEsouToavSMbd-V5EenrrIunnaDZAsB0D_9bk-mhnvQFu9o0HYC8cjn7XLn07MoZJ-8Q_bsC7iF7X42FRtAdoiKeJPnF1R9PcLZlw2HqW4jue24k5bQF70FqGcWkoYqOERCZitnnXzJtMs2X8DVwGYQDyrwusMWx5w9L51uUGoJ6aDBpS3Q-XZkfFUWYjqnwhAtCjPZSGftp419dCEoMJYIGya3luIIxa3yPZ0CtYroienWs2fyXMhrR1I3JJzy1xgcSRJ1g2IeG4JxsLjp_O55JfhY_ep-5jAX-4X3VreLP85_zlthA4Xle576YlkqwedqV4pYr=w397-h809-no">
         
splash screen

         <img src="https://lh3.googleusercontent.com/x7DP1lXMNTGPcPHcc5FwHSAULAFzWJCWifuo4C4fHqH5GRixNQpY5n9X-oog1bF1ZKzBE3Al9WgKSD-ife0TVLtUA_4YKT3vojrdspZFSNZzwYleWVlkRRHelO8yU3yK6A4rYDnLTvSD5HvbGi0m6DXvJ3sgsJlVlM47kel5IrEzbEmLbNtX8FajqvWa0p25kKED58oUppjUuHgcLgJ96s2Ho-pfZBBRDDjE6bo9ESDfywKnBzLL0cmLSD_6NVm7rvXbQ2XODd6N2qxmo9Dpn1uwS5WDWqnUQHKsHi11OKQfhaHd5aevucj9GhnT1t82d7y70Ic8w9xZSg07WJTNoSuvPq4fK_aUm1ReaI9tHoMWFQNz81URpGP1Dq-uy6a-WHCyfcSsnRevXTXr5-cld-otOv9pRFvUH4Zqmux6-s50C1Crj6wwjIB24Sj3yzvFnz1Kc1YU7eZCC5G04EdmUKgKcrvy92N6kLYSNYrSF-eDKUoSxIKEbkMBQKfWE7_vSZY4R-h5kCDA5M1zOR09NU-5okcF4whpdBeQGUo0xxlcJ_EGEe3avMwaZ7iu6WPfDYkjvSkc4e_9coYrh6gwm-sIo4GopW9ihSyMqqkHL0P79KYoo8AR9C-FDumn_MwjY6Tyyv_j-iMTT0LNUuyYPCjQ=w398-h820-no">
         
<table>

   <tr>
				 
                              <th class="img-circular"><a href="Aquapella.html"><img src='<img src="https://lh3.googleusercontent.com/cQIRHLw4tMgxcnBRBk069VYGuHjXhs8tOFfX31sSMS6I3JKz8RYmomvIHgnJwx8d-MCZ19jnFB3CqSlkrv98eIflWSQoSxSiey5KSfG__qnuP6AlBwmuxKnzoG-Crsiid3uuFnX5yV_q7EGcKsTKXVA2YePr5cRtNN0jsw_VcSALVJHv1x9dVKSHWJSEXIUOuP-i7c6IxjiG4DooAHThoW7XrANJmYvn78sWBPlXMl-GPX645Pe_XzKlQs7HhsT0o5U8I29J-S6q70qEmPm5G5KQKqHe6EaRryKLtWxvTo-9haqKid6IpM_WRWIDPcPS9CixaGxJXeLmtVrFFUFJaEVA6jDUDM40exAUVwvUdljmxt-GHIRztsJz-FrBQ0hAUDNbLzua39ufL-8Yk-SlpeSSuzufTT2s8dFv7mbPg5tnNtVQkIz00k-w5eDASy9UTwMeaETo_IXpipluf7MJjTYjadeQM5SLkaFby7aOogin9agb06W1FLc6XOhFSXkLV7uWPG6HtgB3ml_FASqMlOPBkVmpnwO3ABZHvurBLYxLWU37BSwFmbITcb-X2JdgNTDrs-BpYCtT-ARhvsZZtF8FAw8lK0RDkQIQoYcdE1TJm9Sbn8ezo1GVPcRcuTD0JsmGWBf0iqjlyG_os8lnMY7Q=w397-h813-no">'      /></a></th>
                              <th><a href="Aquapella.html"><img src='<img src="https://lh3.googleusercontent.com/kTnr_fNnhtGrHDWnTOksFnVVuYMCpTTQuXVzfk9KDs5a93bQoaBinn38DJy3xYjkaZnzfgrnsDUSbzsVRRDBqKRpuz6FWbbguIFooJzHC4SrskjTEh04iJ9UEqivkmWp8EE-s_RtIuqOojPNzzpQH2TZ-mHq5R4s3QTFhf-9dFUF5ArzwaK3XPUaQpqvpLrmxZZMa9qspcC1R2glNChPOrosFwkGbdFvx6BptOYxICj3CZPGNgkP6QoXapRpu7MevgWPclmve3Bea4oaSgTxXpdDJfyR5qwHbos5G-gxIczQVPl8wbMaaC88-Ke4Um7jyoEDUq7bxKPYwdj2QTmGMTRM6XcXzya0pMj8yIhfzgz903QkHkduR_v-K-ZT6PJKgB2knfx0EC6gGghXVrusZVsXh0r_yEexlDUFhI1GinzFsuNOckXhg47B0_3oex_exsgloBWz93oCrkrkOny3qpulw-Tssy1-yc-QDQ1DHUfVjSjt7GyLg-Uc_WfEyGc3Om7177qCjhYv2-2IlpYQt2RHPCgBFZrsq8oajP6Y-8krGDlrklv1ou8gsnZ2d7gaDGxGooFTzze3LgE7t5UAxlHzTLPX5dcPbnzj4TZ5MJ6NIIQ9Q_0NPALr4i9Vz8ZJuX_mQcXqsrzZWNfFfqc1-GkN=w409-h816-no">'      /></a></th>
   </tr>
   </table>      
---


### Architecture
<br>
<br>
<br>

 <img src="https://lh3.googleusercontent.com/Fd9-6wJoZu_JKMzwslRVvgecDp-zYPSjV3cYUvIkmkKJFY5Yo6fpKMbCpLvVuYqQ8sjyacf2BACgbZaN5Za4LEmtF_5onWMeHIGhdUFOS9n2YZNW1uo4n8VuUXDl8yZC_1u043LaSUomzC2sfFlQCE9c3aUWHli9n1UDfG7sIXkJMlWV2whl3daDqYE71gkQeMWz10XYsV-V5PY7sc0q4NgtCt9Z5akadpIX0HjDUNdDjzxtFDOk9XCOenuJhdkGNDFiMCMEIpmaEVG7wgV7GiM3_TQ7z3WGc5TFahSaHERx_FehC6bdsfTICPMX9zfi6l4SnPjUg2GvZhBVdCcO4kSKc3KAReoQA_H9AzsAbJn91nF-_ngbQxRdxlGvb2z2toC0lrfRvYdei5d732i3BgAhSg4o2R1L5QxbfRLzxkgTXmW7CP-XkU3TilCZRXDkUjUCA-QfIS4nCRwyOi7KGmSeXlRTtOtMkJX9wzOvApn058c8zSmi0G90FV-Ln-pKb3DNkZProCKIH9X9UjiKY1U2XxKiPfByf5CfCTP9qROMRuO-ULk4knZ84AZO4XOIc5cxy6WuQWEjZ4lnRzYvQS2tzzqSOUvyya6tK8G6sO7iA7CQewMQpqXIJ8U75KmmIBqgwHrrl8v5VNaTuA=w805-h600-no" alt="Architecture" style="width: -moz-available;">


---
