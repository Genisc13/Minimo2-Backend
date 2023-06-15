Alcance de Desarrollo:
Bien, después de la reunión que Tuvimos con Toni el pasado 13 de junio, dediqué varias horas a implementar el login y el registro utilizando google, en este readme, como en el README de web, voy a explicar en que lugares apliqué cambios exactamente para poder permitir el login y el registro usando una entidad externa como en google:
- /src
    - /application
    En userUseCase.ts cree getUserByEmail y loginFrontendGoogleUser que uno se usa para poder obtener el usuario sabiendo el mail y el loginFrontendGoogleUser es un login pero sin hacer comparación de hashes, ya que la contraseña que sacamos de la base de datos ya tiene un hash irreversible, por tanto la comparación vendrá solo si el email és correcto. Esto se hace ya que, si se han podido obtener las credenciales de google, es que efectivamente eres un usuario propietario de esa cuenta de google, lo que permite que se pueda hacer el login de este modo.
    - /domain/user
    getUserByEmail(email:string):Promise<UserEntity|null>;
    loginFrontendGoogleUser(data:AuthEntity):Promise<String[2]|null>;
    Estas dos lineas fueron añadidas en el user.repository.ts
    - /infraestructure/controller
    En user.ctrl.ts fueron añadidos:

    this.loginFrontendGoogleUserCtrl=this.loginFrontendGoogleUserCtrl.bind(this); en la linea 22
    this.getUserByEmailCtrl= this.getUserByEmailCtrl.bind(this); en la linea 16
    Además se añadieron las funciones justo debajo:
    getUserByEmailCtrl
    loginFrontendGoogleUser

    - /infraestructure/repository
    En mongoUser.repository.ts añadí las funciones getUserByEmail
    loginFrontendGoogleUser
    Donde se hace el ataque a mongoDB, nos podemos fijar que en loginFrontendGoogleUser no se hace la comprovación de hashes de contraseñas por lo explicado anteriormente
    - /infraestructure/route
    en user.route se añaden la rutas correspondientes:
    routeUser.get("/user/google/check/:mailUser",userCtrl.getUserByEmailCtrl),
    routeUser.post('/user/loginfrontendgoogle',userCtrl.loginFrontendGoogleUserCtrl);
Una vez hechos estos cambios, la logica de Backend para hacer el Registro en Google está hecha
