getfacl
setfacl -Rdm u:x:rw,g:x:rwx

teachers teachers:r t1:rw

students teachers:rw students:r

ESO1 teachers:rw eso1:r

ESO2 teachers:rw eso2:r

stdents,eso1: s1
students,eso2: s2
students: s3

Verificar
s1 no puede leer ESO2
crea carpeta matematicas eso1 y comprobar que alumno puede entrar
t2 no escritura teachers
