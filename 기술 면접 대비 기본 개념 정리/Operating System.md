# Operating System

π **Contents**

- Operating System
  - [μ΄μμ²΄μ λ](#μ΄μμ²΄μ λ)
  - [μ»€λ](#μ»€λ)
  - [νλ‘μΈμ€μ μ€λ λ](#νλ‘μΈμ€μ-μ€λ λ)
  - [non-blocking callμ΄λ](#non-blocking-callμ΄λ)
  - [λ°λͺ¬(daemon) νλ‘μΈμ€λ](#λ°λͺ¬-νλ‘μΈμ€λ)
  - [μ»΄ν¨ν° κ΅¬μ‘°](#μ»΄ν¨ν°-κ΅¬μ‘°)


* * *

## μ΄μμ²΄μ λ
- **μ΄μμ²΄μ (Operating System)λ, μ»΄ν¨ν° νλμ¨μ΄ λ°λ‘ μλ¨μ μ€μΉλλ μννΈμ¨μ΄λ₯Ό μλ―Ένλ€. κ·Έλμ μ¬μ©μ λ° λ€λ₯Έ λͺ¨λ  μννΈμ¨μ΄μ νλμ¨μ΄λ₯Ό μ°κ²°ν΄μ£Όλ μ­ν μ νλ€.**
- λ°λΌμ, μ μμ μΌ°μ λ μ΄μμ²΄μ κ° μμΌλ©΄ κ·Έ μ»΄ν¨ν°λ κ³ μ²  λ©μ΄λ¦¬μ λΆκ³Όνλ©°, μ΄ κ³ μ² μ λμμν€κΈ° μν΄μ νμν κΈ°λ³Έμ μΈ μννΈμ¨μ΄κ° λ°λ‘ μ΄μμ²΄μ μ΄λ€.
  - μ¬μ©μ μμ₯μμλ νλμ¨μ΄ μμ²΄λ₯Ό λ€λ£¨κΈ° μ½μ§ μμΌλ―λ‘, νλμ¨μ΄ μμ κΈ°λ³Έμ μΌλ‘ μ΄μμ²΄μ λ₯Ό νμ¬ν΄μ μ μμ μΌ°μ λ μμ½κ² μ¬μ©ν  μ μλ μνκ° λλλ‘ νλ κ²μ΄λ€.

- κ°μ’ μννΈμ¨μ΄λ€μ **νλμ¨μ΄μ μ΄μμ²΄μ κ° ν λͺΈμΌλ‘ μ‘΄μ¬νλ "μ»΄ν¨ν° μμ€ν"** μμμ μνλλ κ²μΌλ‘ λ³Ό μ μλ€.

* * *

## μ»€λ
- μ»΄ν¨ν°μ μ μμ μΌλ©΄ μ΄μμ²΄μ λ κ°μ΄ μ€νμ΄ λλ€. κ·Έλ¦¬κ³  κΈ°λ³Έμ μΌλ‘ λͺ¨λ  μννΈμ¨μ΄κ° μ»΄ν¨ν° μμ€νμμ μ€νλκΈ° μν΄μλ λ©λͺ¨λ¦¬μ κ·Έ νλ‘κ·Έλ¨μ΄ μ¬λΌκ° μμ΄μΌ νλ€.
- μ΄μμ²΄μ  μμ²΄λ νλμ μννΈμ¨μ΄λ‘μ μ μ μΌμ§κ³Ό λμμ λ©λͺ¨λ¦¬μ μ¬λΌκ°λ€. νμ§λ§ μ΄μμ²΄μ λ κ·λͺ¨κ° λλ¬΄ ν¬κΈ° λλ¬Έμ μ΄μμ²΄μ  μ€, ν­μ νμν λΆλΆλ§μ μ μμ΄ μΌμ§κ³Ό λμμ λ©λͺ¨λ¦¬μ μ¬λ €λκ³  κ·Έλ μ§ μμ λΆλΆμ νμν  λ λ©λͺ¨λ¦¬λ‘ μ¬λ €μ μ¬μ©νκ² λλ€. 
  - **μ΄ λ, λ©λͺ¨λ¦¬μ μμ£Όνλ μ΄μμ²΄μ μ λΆλΆμ μ»€λ(Kernel)μ΄λΌκ³  λΆλ₯Έλ€.** μ΄ μ»€λμ μ’μ μλ―Έμ μ΄μμ²΄μ λΌκ³ λ νλ€. μ¦, μ»€λμ μ΄μμ²΄μ  μ½λ μ€μμλ ν΅μ¬μ μΈ λΆλΆμ λ»νλ€.


- https://goodmilktea.tistory.com/23


* * *

## νλ‘μΈμ€μ μ€λ λ
- κ³Όκ±°μ μ»΄ν¨ν°λ κ²μμ λ€μ΄λ‘λ νλ μμ, λ§μ°μ€λ ν€λ³΄λλ‘λΆν° μλ ₯μ λ°λ μμ, λΈλΌμ°μ Έλ‘ μ¬μ΄νΈλ₯Ό λμλ€λλ μμ λ± μμμ λμμ νμ§ λͺ»νκ³  ν λ²μ νλμ©λ§ ν  μ μμλ€.
- μ»΄ν¨ν°κ° νλ‘μΈμ€ μ¬λ¬ κ°λ₯Ό ν¨κ» λλ¦¬λ λ©ν°νμ€νΉμ΄ κ°λ₯ν΄μ§κΈ° μ κΉμ§λ μ΄λ° νμμ΄ μΌμ΄λ¬λ€κ³  νλ€.

## νλ‘μΈμ€
- μ»΄ν¨ν°μμ μ€νν  μ μλ νμΌ, μλμ°μ κ²½μ° μ΄λ¦ λ€μͺ½μ .exeκ° λΆμ΄μλ κ·Έλ° νμΌλ€μ **"νλ‘κ·Έλ¨"** μ΄λΌκ³  νλ€. **μ΄λ¬ν νλ‘κ·Έλ¨μ΄ λ©λͺ¨λ¦¬μ μ¬λΌκ°μ νλ‘μΈμ€κ° λκ³  CPUλ₯Ό μ»κ³  λΉΌμκΈ°λ κ³Όμ μ λ°λ³΅νμ¬ νλ‘κ·Έλ¨μ΄ μ§νλλ€.** 
  - μ¦, νλ‘μΈμ€λ μ΄μμ²΄μ μ μ§λ°°λ₯Ό λ°μΌλ©° μνλλ€κ³  λ³Ό μ μλ€.
- νλ‘κ·Έλ¨μ΄ μ€νλμ λμκ°κ³  μλ μν, μ¦ μ»΄ν¨ν°κ° μ΄λ€ μΌμ νκ³  μλ μν / **μ»΄ν¨ν°μμ μ°μμ μΌλ‘ μ€νλκ³  μλ μ»΄ν¨ν° νλ‘κ·Έλ¨μ "νλ‘μΈμ€"** λΌκ³  νλ€. **λν, μ΄μμ²΄μ λ‘λΆν° νλ‘μΈμ, νμν μ£Όμ κ³΅κ°, λ©λͺ¨λ¦¬ λ± μμμ ν λΉλ°λ μμμ λ¨μλΌκ³ λ λ³Ό μ μλ€.**
- μ€λλ  μ°λ¦¬λ€μ΄ μ»΄ν¨ν°λ₯Ό μΎμ νκ² μ¬μ©ν  μ μλ κ±΄, μ΄μμ²΄μ κ° μ¬λ¬ κ°μ νλ‘μΈμ€λ₯Ό ν¨κ» λλ¦¬κ³  μκΈ° λλ¬Έμ΄λ€.
  - ex) μλμ°μ κ²½μ° μμκ΄λ¦¬μλ‘ λ€μ΄κ°μ μ»΄ν¨ν°μ μΌλ§λ λ§μ νλ‘μΈμ€λ€μ΄ μ§κΈλ λκ³  μλμ§ νμΈν  μ μμ.
- μ¬λ¬ νλ‘μΈμ€λ₯Ό ν¨κ» λλ¦¬λ μμμ -> **"λμμ , λ³λ ¬μ , λλ μ΄ λμ νΌν©"**μΌλ‘ μ΄λ£¨μ΄μ§λ€.

### λμμ±
- **νλ‘μΈμ(CPU)** νλκ° μ΄κ±° μ‘°κΈ νκ³  μ΄κ±° μ‘°κΈ νκ³  μ΄λ°μμΌλ‘ μ¬λ¬ μμμ λμκ°λ©΄μ μΌλΆλΆμ© μ§ννλ κ².
  - μ΄λ κ² μ§νμ€μΈ μμμ λ°κΎΈλ κ²μ **"Context Switching"** μ΄λΌκ³  νλλ° μ¬κΈ°μλ λ€μν λ°©μκ³Ό μκ³ λ¦¬μ¦μ΄ μ¬μ©λλ€.  
    μ΄ κ³Όμ μ΄ λΉ¨λ¦¬ λμκ°κΈ° λλ¬Έμ μ¬λλ€μκ²λ μ΄ νλ‘μΈμ€κ° λ§μΉ λμμ μ§νλλ κ²μ²λΌ λκ»΄μ§κ² λλ€.

### λ³λ ¬μ±
- **νλ‘μΈμ νλμ(νλμμ) μ½μ΄ μ¬λ¬κ°**κ° λ¬λ €μ μ½μ΄ κ°κ°μ΄ λμμ μμλ€μ μννλ κ².  
  ex) λμΌμ½μ΄, μΏΌλμ½μ΄, μ₯νμ½μ΄ λ± μ΄λ° λͺμΉ­μ΄ λΆλ λ©ν°μ½μ΄ νλ‘μΈμκ° μ₯μ°©λ μ»΄ν¨ν°μμλ§ ν  μ μλ λ°©μ.
- CPUμ μλκ° λ°μ΄ λ± λ¬Όλ¦¬μ  μ μ½ λλ¬Έμ μμ λ§νΌ λΉ λ₯΄κ² λ°μ νμ§ λͺ»νμ, κ·Έ λμμΌλ‘ μ½μ΄λ₯Ό μ¬λ¬ κ° λ¬μμ μμμ λΆλ΄ν  μ μλλ‘ λ§λ  κ²μ΄λ€.


## μ€λ λ
- μ»΄ν¨ν°λ νμ¬ μ¬λ¬ νλ‘μΈμ€λ₯Ό ν¨κ» λλ¦΄ μ μκ² λμμ§λ§, μ΄κ±Έλ‘ μΆ©λΆν κΉ?
- λΈλΌμ°μ λ νλμ νλ‘κ·Έλ¨μ΄κ³  μ΄κ² λλ©΄μ νλμ νλ‘μΈμ€κ° μ§νλκ³  μλ€.     
  κ·Έλ°λ° λΈλΌμ°μ κ° μΌμ ν  λλ, λΈλΌμ°μ  μμμλ κ²μμ λ€μ΄λ°λ λμμ λ€λ₯Έ νμ΄μ§λ€μ λμλ€λ μ μμ΄μΌ νκ³  μ νλΈ μμμ λ°μ΄ν°λ₯Ό λ°μμ€λ©΄μ λ°μμ§ λ°μ΄ν°λ‘ μμμ μ€νν  μλ μμ΄μΌ νλ€.   
  -> ν νλ‘μΈμ€ λ΄μμλ μ¬λ¬ κ°λμ μμλ€μ΄ λμμ μ§νλ  νμκ° μλ κ²μ΄λ€.   
  -> **μ¦, νλ‘μΈμ€ λ΄μμ μ€νλλ μ¬λ¬ νλ¦μ λ¨μλ₯Ό "μ€λ λ"** λΌκ³  λΆλ₯Έλ€.

- **λ©ν° νλ‘μΈμ€λ‘ ν  μ μλ μμλ€μ κ΅³μ΄ νλμ νλ‘μΈμ€μμ μ€λ λλ‘ λλ μ μΌμ νλ μ΄μ λ, μ΄μμ²΄μ λ μμ€ν μμμ ν¨μ¨μ μΌλ‘ κ΄λ¦¬νκΈ° μν΄μ μ€λ λλ₯Ό μ¬μ©νκΈ° λλ¬Έμ΄λ€. λν, νλ‘μΈμ€ κ°μ ν΅μ λ³΄λ€ μ€λ λ κ°μ ν΅μ  λΉμ©μ΄ μ μ΄ μμλ€ κ°μ ν΅μ  λΆλ΄μ΄ μ€μ΄λ€κ² λλ€.**

ex) νλ‘μΈμλ μλ¦¬μ¬λΌκ³  κ°μ νλ€λ©΄, λλμ£Όλ¬Έμ΄ λ€μ΄μ€λ μλΉμμ λμμμ΄ λ§λ€μ΄λ΄λ μλ¦¬ λ©λ΄ νλνλκ° λ°λ‘ νλ‘μΈμ€λΌκ³  ν  μ μλ€.  
    μ»΄ν¨ν°λ νλ‘μΈμ€λ§λ€ μμμ λΆν ν΄μ ν λΉνκ² λλ€.  
    λΌλ©΄ λμ΄λ μΉμ, κΉλ°₯ λ§λ μΉμ, νλ²κ±° λ§λλ μΉμ λ± μ΄λ κ² μ‘°λ¦¬ κ³΅κ°μ λλ μ μλ¦¬μ¬ νΌμ λμλ€λλ©΄μ λμμ μΌλ‘ νλ  μ¬λΏμ΄μ λ³λ ¬μ μΌλ‘ νλ  νΉμ μ΄ 2κ°μ§ λ°©λ²μ μμ΄μ νλ  μ΄ λ©λ΄λ€μ κ³μν΄μ λ§λ€μ΄ λκ°λ κ²μ΄λ€.  
    νλ²κ±°λ₯Ό λ§λλ νλ‘μΈμ€μμλ ν¨ν°λ₯Ό κ΅½λ μ€λ λκ° μ§νλλ λμ, λΉ΅μ μΌμ±λ₯Ό μΉκ³  μμ€λ₯Ό λΏλ¦¬λ μ€λ λλ μ§νλ  μ μμ κ²μ΄λ€.  
    -> μ΄μ²λΌ ν λ©λ΄μ μ€λ λλ€μ κ°μ μ‘°λ¦¬λμμ μ§νλλ€. κ°μ λ©λ΄λ₯Ό λ§λ€λλ κ°μ κ³΅κ°κ³Ό μ₯λΉ, μ¦ κ°μ μμμ κ³΅μ νλ κ²μ΄ λ ν¨μ¨μ μ΄κΈ° λλ¬Έμ΄λ€. νλ‘μΈμ€λ€μ μ»΄ν¨ν°μ μμμ λΆν ν΄μ μ°μ§λ§, μ€λ λλ νλ‘μΈμ€λ§λ€ μ£Όμ΄μ§ μ μ²΄ μμμ ν¨κ» μ¬μ©νλ κ²μ΄λ€.
    
μ΄λ¬ν λ°©λ²μ΄ μλμ ν¨μ¨λ©΄μμλ μ’κ² μ§λ§, λ¨μ λ μ‘΄μ¬νλ€.  
νλ‘μΈμ€ μμμ κ³΅μ λλ λ³μμ μ€λ λ 2κ°κ° λμμ μμ λλ€λ©΄, μ»΄ν¨ν°μμλ μλ¬κ° λκ² λλ€.  
μ΄λ κ² μκ° λ¬Έμ λ‘ λ°μνλ μ΄λ° μν©λ€μ μμνκ³  λ°©μ§ν΄μΌ νκΈ° λλ¬Έμ μ€λ λλ₯Ό μ¬μ©νλ νλ‘κ·Έλλ°μ μ½λλ₯Ό μ§κΈ°λ, λλ²κΉμ ν΄μ μ€λ₯λ₯Ό μ°Ύμλ΄κ³  μμΈμ λ°νκΈ°λ κ΅μ₯ν κΉλ€λ‘μ΄ κ²½μ°κ° λ§λ€.

- λ€ννλ μ΄λ° μμλ€μ λ μ½κ³  μμ νκ² ν  μ μλ λκ΅¬λ€μ΄λ νλ‘κ·Έλλ° λ°©μλ€μ΄ μ€λλ μ κ°λ°μλ€μ λ§μ΄ λμμ£Όκ³  μλ€.


### ex) Javaμμ μ€λ λ μ¬μ©νλ νλ‘κ·Έλλ°
- λΌλ©΄ λμ΄λ μΉμμ λ²λκ° 4κ° μλ€λ©΄ -> μ€λ λλ 4κ°λ₯Ό λ§λ€μ΄μ λΌλ©΄μ΄ μ΅λ 4κ°κΉμ§ λμμ λμ¬μ§λλ‘ νλ‘κ·Έλλ° ν  μ μλ€. 
- https://www.youtube.com/watch?v=iks_Xb9DtTM&list=LL&index=9&t=4s ν΄λΉ μμμ²λΌ μ€λ λλ₯Ό μ¬μ©νλ μ½λλ€μ μ΄ν΄λ³΄κ³  μ§λ³΄λ©΄μ μ΄ν΄νκ³  λλ©΄, μ€λλ  μ¬μ©λλ λμμ± νλ‘κ·Έλλ°μ λ³΄λ€ λ μ νμ©ν  μ μλλ‘ νλ κΈ°μ΄μ§μμ΄ λλ€. 


* * *

## non-blocking callμ΄λ
- **non-blocking λ°©μμ I/Oμμμ μ§ννλ λμ μ μ  νλ‘μΈμ€μ μμμ μ€λ¨μν€μ§ μκ³ , μ μ  νλ‘μΈμ€κ° I/Oλ₯Ό μ²λ¦¬νκΈ° μν΄ μ»€λμ ν¨μλ₯Ό νΈμΆνλ system callμ΄ μ§νλλ©΄ μ»€λμμ ν¨μμ μ§ν μν©κ³Ό μκ΄μμ΄ λ°λ‘ κ²°κ³Όλ₯Ό λ°ννλ κ²μ μλ―Ένλ€.**
- μ¦, μ νλ¦¬μΌμ΄μ μ€ν μ μ΄μμ²΄μ  λκΈ° νμ λ€μ΄κ°μ§ μκ³  μ€ν μ¬λΆμ κ΄κ³μμ΄ λ°λ‘ μλ΅μ λ³΄λ΄λ κ²½μ°λ₯Ό μλ―Ένλ€. 
  - λ€λ₯Έλ§λ‘ νλ©΄ νΈμΆλ ν¨μκ° λ°λ‘ λ¦¬ν΄ν΄μ νΈμΆν ν¨μμκ² μ μ΄κΆμ λκ²¨μ£Όκ³ , νΈμΆν ν¨μκ° λ€λ₯Έ μΌμ ν  μ μλ κΈ°νλ₯Ό μ€ μ μλ€λ©΄ non-blocking λ°©μμ΄λ€.

- **I/Oλ,** μλ ₯(Input)/μΆλ ₯(Output)μ μ½μλ‘ μ»΄ν¨ν° λ° μ£Όλ³μ₯μΉμ λ°μ΄ν°λ₯Ό μ μ‘νλ νλ‘κ·Έλ¨, μ΄μ νΉμ μ₯μΉλ₯Ό μΌμ»«λ λ§μ΄λ€. ν€λ³΄λμ λ§μ°μ€μ²λΌ μλ ₯μ μν κΈ°κΈ°, νλ¦°ν°μ²λΌ μΆλ ₯μλ§ μ¬μ©λλ κΈ°κΈ° λ± λ€μνλ€.
- **μ»€λμ΄λ,** λ©λͺ¨λ¦¬μ μμ£Όνλ μ΄μμ²΄μ μ μΌλΆλΆμ μλ―Έ.

- [κ΄λ ¨ μλ£](https://yoonucho.github.io/review/2019/02/26/sync_async_blocking_non-blocking.html)
  - [2](https://asfirstalways.tistory.com/348)


* * *

## λ°λͺ¬ νλ‘μΈμ€λ
- https://blogger.pe.kr/770


* * *

## μ»΄ν¨ν° κ΅¬μ‘°
- **μ»΄ν¨ν°μμ νΉμ  νλ‘κ·Έλ¨μ μ€ννλ©΄, λ³΄μ‘°κΈ°μ΅μ₯μΉ(νλλμ€ν¬)μ μ μ₯λ λ°μ΄ν°λ₯Ό μ£ΌκΈ°μ΅μ₯μΉ(λ©λͺ¨λ¦¬)λ‘ λΆλ¬μ CPUκ° ν΄λΉ λ°μ΄ν°λ₯Ό μ²λ¦¬νλ κ³Όμ μΌλ‘ μ€ν**
- CPUλ μλ¦¬μ¬, λμ€ν¬λ λμ₯κ³ , λ©λͺ¨λ¦¬λ λλ§λΌκ³  λΉμ ν΄λ³Ό μ μλ€.
  - **CPU**
  - **μΊμ λ©λͺ¨λ¦¬**
  - **μ£ΌκΈ°μ΅μ₯μΉ(λ©λͺ¨λ¦¬)**
  - **λ³΄μ‘°κΈ°μ΅μ₯μΉ(λμ€ν¬)**

- **CPU** : μ»΄ν¨ν°μ κ΄λ ¨λ λͺ¨λ  λͺλ Ήμ΄λ€κ³Ό μμλ€μ μννλ μ­ν . λ©λͺ¨λ¦¬λ‘λΆν° λ°μ΄ν°λ₯Ό κ°μ Έμ€κ³ (Fetch) λͺλ Ήμ΄λ₯Ό λ μ§μ€ν°λΌλ κ³³μ μ μ₯νλ€. κ·Έλ¦¬κ³  λμ½λ©(Decoding) κ³Όμ μ κ±°μ³ λͺλ Ήμ΄λ₯Ό μλμ μν μ νΈλ‘ λ³ννλ€. λ§μ§λ§μΌλ‘ μ€ν(Execute)λ¨κ³λ₯Ό κ±°μΉλ€.

- **μΊμ λ©λͺ¨λ¦¬** : CPUμ μ£ΌκΈ°μ΅μ₯μΉ μ¬μ΄μ μλ μ°¨μ΄λ₯Ό μνμν€κΈ° μν κ³ μ λ²νΌ(μμ) λ©λͺ¨λ¦¬λ‘, μ£ΌκΈ°μ΅μ₯μΉμ CPUμ¬μ΄μ μμΉνλ©°, μμ£Ό μ¬μ©νλ νλ‘κ·Έλ¨κ³Ό λ°μ΄ν°λ₯Ό κΈ°μ΅νλ€. μ΄λ¬ν μΊμλ©λͺ¨λ¦¬λ₯Ό μ¬μ©νλ©΄ μ£ΌκΈ°μ΅μ₯μΉλ₯Ό μ κ·Όνλ νμκ° μ€μ΄λ€μ΄ μ»΄ν¨ν°μ μ²λ¦¬μλκ° ν₯μλλ€.

- **μ£ΌκΈ°μ΅μ₯μΉ - λ©λͺ¨λ¦¬** : μ»΄ν¨ν° λ΄λΆμμ νμ¬ CPUκ° μ²λ¦¬νκ³  μλ λ΄μ©μ μ μ₯νκ³  μλ κΈ°μ΅μ₯μΉμ΄λ€. λνμ μΌλ‘ ROMκ³Ό RAMμΌλ‘ κ΅¬λΆλλ€.
  - **ROM** : ROMμ μ μμ΄ κΊΌμ Έλ λ°μ΄ν°λ€μ΄ μ¬λΌμ§μ§ μλ λΉνλ°μ± λ©λͺ¨λ¦¬μ΄λ€. μ€μ§ κΈ°μ΅λ λ°μ΄ν°λ€μ μ½κΈ°λ§ κ°λ₯νλ€.
  - **RAM** : RAMμ ROMκ³Ό λ¬λ¦¬ μ½κ³  μ°κΈ°κ° κ°λ₯ν κΈ°μ΅μ₯μΉμ΄λ€. νμ§λ§ μ μμ΄ λμ΄μ§λ©΄ λ°μ΄ν°κ° μ λΆ μ§μμ§λ νλ°μ± λ©λͺ¨λ¦¬μ΄λ€. 

- **λ³΄μ‘°κΈ°μ΅μ₯μΉ - λμ€ν¬** : λ¬Όλ¦¬μ μΈ λμ€ν¬κ° μ°κ²°λμ΄μλ κΈ°μ΅μ₯μΉλ‘, μ£ΌκΈ°μ΅μ₯μΉ λ³΄λ€λ λλ¦¬μ§λ§ μ»΄ν¨ν° μ μμ λλλΌλ μ μ₯λ λ°μ΄ν°κ° μ¬λΌμ§μ§ μκ³  μκ΅¬μ μΌλ‘ λ³΄κ΄ν  μ μλ μ₯μΉμ΄λ€. λνμ μΌλ‘ HDDμ SSDκ° μλ€.
  - **HDD** : λ¬Όλ¦¬μ μΈ λμ€ν¬λ₯Ό κ³ μμΌλ‘ νμ μμΌ λ°μ΄ν°λ₯Ό μ μ₯νλ μ₯μΉμ΄λ€. λμ€ν¬λ₯Ό λ¬Όλ¦¬μ μΌλ‘ μ μ₯νκΈ° λλ¬Έμ μΆ©κ²©μ μ½νκ³  μμμ΄ λ°μνλ€. μ΅κ·Όμλ λ§μ΄ μ¬μ©λμ§ μκ³  μλ€.
  - **SSD** : SSDλ λ°λμ²΄ κΈ°λ°μ μ λ³΄λ₯Ό μ μ₯νλ μ₯μΉμ΄λ€. λ¬Όλ¦¬μ μΌλ‘ λ°μ΄ν°λ₯Ό μ μ₯νμ§ μκ³  μ κΈ°μ μΌλ‘ λ°μ΄ν°λ₯Ό μ μ₯νκΈ° λλ¬Έμ HDDμ λΉν΄μ μλκ° λΉ λ₯΄κ³  μμμ΄ λ°μνμ§ μλλ€. 

- [κ΄λ ¨ λΈλ‘κ·Έ](https://coding-factory.tistory.com/357)
- [κ΄λ ¨ λΈλ‘κ·Έ 2](https://mindstation.tistory.com/152)





