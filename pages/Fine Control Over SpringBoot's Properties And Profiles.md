- #[[excerpt]] [Fine Control Over SpringBoot's Properties And Profiles | by Thiago Almeida | ITNEXT](https://itnext.io/fine-control-over-springboots-properties-and-profiles-25068bf25f1f)
- tags: #[[SimpRead]]
- read date: [[2022_06_14  ]]
- desc: Control your SpringBoot’s properties over different profiles with great ease.
- [📌](<http://localhost:7026/reading/14?title=Fine Control Over SpringBoot's Properties And Profiles - by Thiago Almeida - ITNEXT#id=1655189644331>)  you can split a .properties file’s content into several logical parts. And with [Activation Properties](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#features.external-config.files.activation-properties), you can add logic to enable/disable each part.
  
  You can define a single .properties file like this:
  
  ```
  # application.propertiesproperty=default
  spring.profiles.active=unique
  #---
  spring.config.activate.on-profile=unique
  property=unique
  ```
  
  Note that this is a single `application.properties` file.
  
  And now your fancy code prints `unique` . 🎉🎉🎉
- [📌](<http://localhost:7026/reading/14?title=Fine Control Over SpringBoot's Properties And Profiles - by Thiago Almeida - ITNEXT#id=1655189658107>)
- [📌](<http://localhost:7026/reading/14?title=Fine Control Over SpringBoot's Properties And Profiles - by Thiago Almeida - ITNEXT#id=1655189669338>)  If you want to activate it in several profiles, you can define it like this:
  
  ```
  spring.config.activate.on-profile=local | dev
  ```
- [📌](<http://localhost:7026/reading/14?title=Fine Control Over SpringBoot's Properties And Profiles - by Thiago Almeida - ITNEXT#id=1655189675569>)  If you **don’t** want to activate it in a specific profile, you can do it like this:
  
  ```
  spring.config.activate.on-profile=!unique
  ```
- [📌](<http://localhost:7026/reading/14?title=Fine Control Over SpringBoot's Properties And Profiles - by Thiago Almeida - ITNEXT#id=1655189682954>)  *   For YAML files, the separator is just `---` . That’s a standard YAML thing.