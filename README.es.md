# gdmty_django_recaptcha_enterprise

** Biblioteca para integrar Google reCaptcha Enterprise en Django **

Esta biblioteca es un borrador pero funciona. Se hizo porque no hay una biblioteca para Django que implemente Google's reCaptcha Enterprise. Pero ahora esta biblioteca proporciona una forma de verificar tokens de reCaptcha Enterprise.

## Instalación:

```bash
pip install gdmty-django-recaptcha-enterprise
```

## Uso:

En settings.py:

```python
# Importar service_account de google.oauth2 e instanciar un objeto Credentials desde la cuenta de servicio
from google.oauth2 import service_account

# Agregar 'gdmty_django_recaptcha_enterprise' en INSTALLED_APPS
INSTALLED_APPS = [
    ...,
    'gdmty_django_recaptcha_enterprise',
    ...
]

# Configurar las siguientes variables
RECAPTCHA_CREDENTIALS_SERVICE_ACCOUNT_FILE = os.path.join(BASE_DIR, "_gcp_sa", "recaptcha_enterprise_service_account_key.json")
credentials = service_account.Credentials.from_service_account_file(RECAPTCHA_CREDENTIALS_SERVICE_ACCOUNT_FILE)
RECAPTCHA_ENTERPRISE_PROJECT_ID = 'your-project-id'
RECAPTCHA_ENTERPRISE_SERVICE_ACCOUNT_CREDENTIALS = credentials
RECAPTCHA_ENTERPRISE_SITE_KEY_VERIFY = 'your-site-key' # This one is the site key for usage with seamless verification with the reCaptcha Enterprise API withouth user interaction
RECAPTCHA_ENTERPRISE_SITE_KEY_CHALLENGE = 'your-site-key' # This one is the site key for usage with challenge verification with the reCaptcha Enterprise API with user interaction
RECAPTCHA_ENTERPRISE_BYPASS_TOKEN = 'your-bypass-token' # Optional, only for debug and development usage. For production must be False. When DEBUG=False this must be False too or will not pass assesments never.
```

En el código:

```python
# Importar assess_token de gdmty_django_recaptcha_enterprise.recaptcha, luego puede usarse para evaluar tokens donde lo necesites. En este ejemplo mostramos una vista hipotética que recibe un token de una solicitud POST.
# Ahora entonces, puede usarse el decorador requires_recaptcha para verificar el token antes de que se ejecute la vista.

from gdmty_django_recaptcha_enterprise.recaptcha import RecaptchaEnterprise
from gdmty_django_recaptcha_enterprise.decorators import requires_recaptcha
from django.conf import settings


recaptcha = RecaptchaEnterprise(
    settings.RECAPTCHA_ENTERPRISE_PROJECT_ID, 
    settings.RECAPTCHA_ENTERPRISE_SITE_KEY_VERIFY, 
    settings.RECAPTCHA_ENTERPRISE_SERVICE_ACCOUNT_CREDENTIALS)

# Cada vez que necesite evaluar un token, puede usar el método assess_token del objeto recaptcha, puede usarse desde este decorador usando el parámetro de acción para evaluar.
# Cuando se usa el decorador, el decorador maneja la solicitud para obtener el token de la solicitud y evaluarlo, si el token es válido, se ejecuta la vista, si no, la vista no se ejecuta y se devuelve una respuesta 403.

@requires_recaptcha(action='action-to-verify')
def my_view(request):
    ...
    pass
    ...
```
