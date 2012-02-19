# Play 2.0 tips and tricks


## To use Play Framework 2 with Twitter Bootstrap 2 :

* Build Play from last sources (near RC3) (Since less version has been updated to 1.2 )
* copy bootstrap's less files into `app/assets/stylesheets/bootstrap`
* ad `bootstrap/_` before each `bootstrap.less` import declaration and rename each file the same way ( This is the convention to not compile theses files while packaging compiled assets )
* move `bootstrap.less` file into app/assets/stylesheets 

Can be done with a script like this ran into the app/assets/stylesheets/bootstrap
```bash
for a in *.less; do mv $a _$a; done 
sed -i '|s|@import "|@import "bootstrap/_|' bootstrap.less 
mv bootstrap.less ../bootstrap.less`
```
