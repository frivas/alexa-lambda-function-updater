![Amazon Console](https://github.com/frivas/alexa-lambda-function-updater/blob/master/imgs/AmazonWebServices.png)

# Tutorial: Cçomo actualizar la función lambda de tu skill de Alexa utilizando AWS CLI

Es bastante común al momento de desarrollar nuestra *skill* de Alexa hacer pruebas locales y a su vez hacer muchos cambios para reparar cualquier error que detectemos. Cuando trabajamos en la función Lambda asociada a nuestro *skill* de Alexa que utiliza otros módulos bien sean de NodeJS o Python el editor inline tiene ciertas limitaciones por lo que el equipo de Lambda de AWS ofrece una alternativa, utilizando AWS CLI para actualizar la función Lambda. En este artículo describo los pasos que debemos seguir para lograr esto.

## Este proceso consta de 4 pasos:

1. Configurar un usuario que tenga permisos para modificar Lambda
2. Instalar AWS CLI
3. Configurar AWS CLI con la información del usuario 
4. Ejecutar el script de actualización de la función

## Paso #1: Configurar un usuario en IAM

- Visita: [Amazon Console](http://console.aws.amazon.com/)

Puedes hacer click sobre el enlace de IAM. En caso de no ver directamente la función puedes escribir "iam" en el cuadro de texto y filtrar automáticamente.

![Amazon Console](https://github.com/frivas/alexa-lambda-function-updater/blob/master/imgs/AmazonConsole.png)

- Luego, por defecto **Dashboard** es la opción por defecto. En este caso nos interesa **Users**(1) y luego hacer click en el botón **Add User**(2).

![Add User](https://github.com/frivas/alexa-lambda-function-updater/blob/master/imgs/AddUser.png)

- Inserta un nombre de usuario(3), preferiblemente uno que te permita distinguir su utilzación para Lambda. Asegurate de que la casilla **Programmatic Access**(4) esta marcada. Esto es muy importante.

**De nuevo, cerciórate de que la casilla Programatic Access está marcada** ;)

Ahora sí, click en **Next: Permissions**(5)

![Add User Info](https://github.com/frivas/alexa-lambda-function-updater/blob/master/imgs/AddUserInfo.png)


- Ahora es momento de permisos. Click en **Attach existing policies directly**(6) por defecto viene marcada la primera opción (esa no la queremos por ahora). Van a aparecer un montón de políticas, sin embargo, nos interesa solo la resultante de aplicar el filtro "LambdaFullAccess"(7) . Marca la casilla que indica "AWSLambdaFullAccess"(8) Luego click en **Next:Review**(9)


![Add User Policies Configuration](https://github.com/frivas/alexa-lambda-function-updater/blob/master/imgs/AddUserPolicies.png)

- En esta parte del proceso, simplemente revisamos que todo esta correcto. Sí, revisamos que todo esta correcto. Listo?. Click, en **Create User**(10)

![Add User Information Review](https://github.com/frivas/alexa-lambda-function-updater/blob/master/imgs/AddUserReview.png)

- Finalmente, una vez que el usuario ha sido creado, podrás ver la información de **Access Key ID** y **Secret Access Key**. Es recomendable descargar el archivo **.csv** con la información. Click en **Close**.

![Add User Success](https://github.com/frivas/alexa-lambda-function-updater/blob/master/imgs/AddUserSuccess.png)

### Paso #1: ✅

## Paso #2: Instalar AWS CLI

Para este paso es necesario tener PIP (pip) instalado. Para verificar si está instalado:

	$ pip -V
	pip 18.0 from /usr/local/lib/python3.7/site-packages/pip (python 3.7)

Si no está instalado, puedes visitar este [enlace](https://gist.github.com/haircut/14705555d58432a5f01f9188006a04ed) en el que encontraras los pasos para ello.

Bien, una vez instalado PIP, procedemos a instalar AWS CLI

	$ pip install awscli --user

Nota: el flag ```--user```se utiliza para poder escribir en los directorios necesarios.

Esto hará que PIP recolecte todas las dependencias de AWS CLI y las instale. La lista de dependencias se puede encontrar en el archivo ```metadata.json``` dentro de ```awscli-V.VV.VV.dist-info```, son:

	botocore==1.12.16
	colorama>=0.2.5,<=0.3.9
	docutils>=0.10
	rsa>=3.1.2,<=3.5.0
	PyYAML>=3.10,<=3.13
	s3transfer>=0.1.12,<0.2.0
	argparse>=1.1

Terminado el proceso de instalación, confirmamos:

	$ aws 
	usage: aws [options] <command> <subcommand> [<subcommand> ...] [parameters]
	To see help text, you can run:
	
	  aws help
	  aws <command> help
	  aws <command> <subcommand> help
	aws: error: the following arguments are required: command

### Paso #2: ✅

## Paso #3: Configurar AWS CLI

Ahora es necesario configurar AWS CLI para que lo podeamos utilizar para actualizar nuestra función Lambda.

Para ello utilizamos **Terminal.app**. 

	$ aws configure
	AWS Access Key ID [None]: <Access Key ID>
	AWS Secret Access Key [None]: <Secret Access Key>
	Default region name [None]: <[1]>
	Default output format [None]: <[2]>

**[1]:** Cuando accedes a la Consola de Amazon en la URL aparece la region que debes colocar. Por ejemplo: ...console.aws.amazon.com/iam/home?region=**eu-west-1**#/home

**[2]:** Es recomendable utilizar "json".

### Paso #3: ✅

## Paso #4: Ejecutar el script de actualización

He creado un pequeño (bastante rudimentario) script que con solo un par de parámetros actualiza la función lambda de nuestra skill de Alexa.

El script esta disponible [aqui](https://github.com/frivas/alexa-lambda-function-updater)

La forma de ejecutarlo es:

	$./lambdaUpdater.sh -z ZIP_FILENAME -f LAMBDA_FUNCTION_NAME

Por ejemplo:

	$./lambdaUpdater.sh -z lambdaFuntion -f CambioHoy

El script hace varias cosas:

- Elimina el archivo lambdaFunction.zip existente
- Crea un archivo lambdaFuntion.zip nuevo, excluyendo todos los archivos y directorios cuyos nombres comiencen por "test" y también el archivo creado por VSCode cuando se crea un nuevo Workspace.
- Utilizando el comando "lambda" de AWS CLI actualiza la función.

El resultado es algo como esto:

	Result:
	
	{
	    "FunctionName": "CambioHoy",
	    "FunctionArn": "arn:aws:lambda:eu-west-1:....",
	    "Runtime": "python3.6",
	    "Role": "arn:aws:iam::.....",
	    "Handler": "index.handler",
	    "CodeSize": 9136434,
	    "Description": "",
	    "Timeout": 3,
	    "MemorySize": 128,
	    "LastModified": "2018-10-02T22:43:18.208+0000",
	    "CodeSha256": ".....",
	    "Version": "$LATEST",
	    "VpcConfig": {
	        "SubnetIds": [],
	        "SecurityGroupIds": [],
	        "VpcId": ""
	    },
	    "TracingConfig": {
	        "Mode": "PassThrough"
	    },
	    "RevisionId": "....."
	}
	
### Paso #4: ✅

Si tienes dudas, sugerencias, correcciones, comentarios o incluso quieres colaborar mejorando el script, no dudes en dejarmelo saber.