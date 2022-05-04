## Authentication, Session 개념
- **루트 디렉터리의 settings.py를 보면, 여러가지 변수들이 있는데 그 중에서도 MIDDLEWARE와 AUTH_PASSWORD_VALIDATORS 이런 부분들을 공부해보자.**

### MIDDLEWARE
- **미들웨어라는 건, 우리가 url pattern을 정의하고 View에 연결시켜서 View에서 요청을 받고 응답을 하는 과정에서 -> View가 요청을 받기 전과 View가 응답을 한 다음에 호출이 되는 것들을 보고 
  미들웨어라고 부른다.**

```python
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
    'debug_toolbar.middleware.DebugToolbarMiddleware',
]
```

- **다음은 settings.py에 정의된 MIDDLEWARE이다. 여기서 SessionMiddleware과 AuthenticationMiddleware 이 2개에 대해 알아보자.**


- **우리의 가상환경 디렉터리안에 모든 코드들을 다 볼 수 있다. 그래서 가상환경 디렉터리 - lib - ... django - contrib - auth - middleware.py로 가서 클릭해보자.**

```python
from django.contrib import auth
from django.contrib.auth import load_backend
from django.contrib.auth.backends import RemoteUserBackend
from django.core.exceptions import ImproperlyConfigured
from django.utils.deprecation import MiddlewareMixin
from django.utils.functional import SimpleLazyObject


def get_user(request):
    if not hasattr(request, "_cached_user"):
        request._cached_user = auth.get_user(request)
    return request._cached_user


class AuthenticationMiddleware(MiddlewareMixin):
    def process_request(self, request):
        if not hasattr(request, "session"):
            raise ImproperlyConfigured(
                "The Django authentication middleware requires session "
                "middleware to be installed. Edit your MIDDLEWARE setting to "
                "insert "
                "'django.contrib.sessions.middleware.SessionMiddleware' before "
                "'django.contrib.auth.middleware.AuthenticationMiddleware'."
            )
        request.user = SimpleLazyObject(lambda: get_user(request))


class RemoteUserMiddleware(MiddlewareMixin):
    """
    Middleware for utilizing web-server-provided authentication.

    If request.user is not authenticated, then this middleware attempts to
    authenticate the username passed in the ``REMOTE_USER`` request header.
    If authentication is successful, the user is automatically logged in to
    persist the user in the session.

    The header used is configurable and defaults to ``REMOTE_USER``.  Subclass
    this class and change the ``header`` attribute if you need to use a
    different header.
    """

    # Name of request header to grab username from.  This will be the key as
    # used in the request.META dictionary, i.e. the normalization of headers to
    # all uppercase and the addition of "HTTP_" prefix apply.
    header = "REMOTE_USER"
    force_logout_if_no_header = True

    def process_request(self, request):
        # AuthenticationMiddleware is required so that request.user exists.
        if not hasattr(request, "user"):
            raise ImproperlyConfigured(
                "The Django remote user auth middleware requires the"
                " authentication middleware to be installed.  Edit your"
                " MIDDLEWARE setting to insert"
                " 'django.contrib.auth.middleware.AuthenticationMiddleware'"
                " before the RemoteUserMiddleware class."
            )
        try:
            username = request.META[self.header]
        except KeyError:
            # If specified header doesn't exist then remove any existing
            # authenticated remote-user, or return (leaving request.user set to
            # AnonymousUser by the AuthenticationMiddleware).
            if self.force_logout_if_no_header and request.user.is_authenticated:
                self._remove_invalid_user(request)
            return
        # If the user is already authenticated and that user is the user we are
        # getting passed in the headers, then the correct user is already
        # persisted in the session and we don't need to continue.
        if request.user.is_authenticated:
            if request.user.get_username() == self.clean_username(username, request):
                return
            else:
                # An authenticated user is associated with the request, but
                # it does not match the authorized user in the header.
                self._remove_invalid_user(request)

        # We are seeing this user for the first time in this session, attempt
        # to authenticate the user.
        user = auth.authenticate(request, remote_user=username)
        if user:
            # User is valid.  Set request.user and persist user in the session
            # by logging the user in.
            request.user = user
            auth.login(request, user)

    def clean_username(self, username, request):
        """
        Allow the backend to clean the username, if the backend defines a
        clean_username method.
        """
        backend_str = request.session[auth.BACKEND_SESSION_KEY]
        backend = auth.load_backend(backend_str)
        try:
            username = backend.clean_username(username)
        except AttributeError:  # Backend has no clean_username method.
            pass
        return username

    def _remove_invalid_user(self, request):
        """
        Remove the current authenticated user in the request which is invalid
        but only if the user is authenticated via the RemoteUserBackend.
        """
        try:
            stored_backend = load_backend(
                request.session.get(auth.BACKEND_SESSION_KEY, "")
            )
        except ImportError:
            # backend failed to load
            auth.logout(request)
        else:
            if isinstance(stored_backend, RemoteUserBackend):
                auth.logout(request)


class PersistentRemoteUserMiddleware(RemoteUserMiddleware):
    """
    Middleware for web-server provided authentication on logon pages.

    Like RemoteUserMiddleware but keeps the user authenticated even if
    the header (``REMOTE_USER``) is not found in the request. Useful
    for setups when the external authentication via ``REMOTE_USER``
    is only expected to happen on some "logon" URL and the rest of
    the application wants to use Django's authentication mechanism.
    """

    force_logout_if_no_header = False
```

- **해당 코드에서 AuthenticationMiddleware 클래스가 바로 우리가 settings.py에서 봤던 것과 동일하다.**
- **settings.py를 보면 'django.contrib.auth.middleware.AuthenticationMiddleware', 이렇게 나와있는데 여기서 Auth... 이렇게 대문자로 되어있기 때문에
  클래스를 가져왔다는 것을 알 수 있다.**
  
- 그래서 이 클래스를 가져왔을 때 실행이 되는 코드를 보면, request를 받을 때 session이 있는지 확인을 하고 처리를 한 다음에 get_user 함수를 실행한다. get_user 함수는 request에 있는 
  session 값을 가지고, 데이터베이스에 있는 session Key를 가지고 데이터를 비교해서 그 유저가 누군지를 가져오는 그 행위를 일컫는 코드를 의미한다. 그래서 request.user = SimpleLazyObject(lambda: get_user(request)) 
  이렇게 request.user에 해당 내용을 넣어준다. **그렇기 때문에 우리가 항상 request.user를 가지고 데이터를 사용할 수 있게 되는 것이다.**
  
  
  
  
  
