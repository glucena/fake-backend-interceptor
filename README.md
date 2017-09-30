# fake-backend-interceptor

## Set up a HttpCient interceptor for backend-less development in Angular 4.3 +

Bellow you can find a very simple way to set up a backend mock that allows you to implement your angular app without having the server side running.

I've built the backend mock as a HTTP_INTERCEPTOR, so every HTTP request will pass trought.

```typescript
import { HTTP_INTERCEPTORS } from '@angular/common/http';
import { HttpInterceptor } from '@angular/common/http';
import { Injectable } from '@angular/core';
import { HttpRequest } from '@angular/common/http';
import { HttpHandler } from '@angular/common/http';
import { Observable } from 'rxjs/Observable';
import { HttpEvent } from '@angular/common/http';


@Injectable()
export class FakeBackendInterceptor implements HttpInterceptor {
   constructor() { }

   intercept(request: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
      if (request.url.endsWith('/users') && request.method === 'GET' && request.params.get('mock')) {
         console.log('HTTP REQEST INTERCEPTED BY THE MOCKED BACKEND');
         request = request.clone({
            url: '/assets/json/users.json'
         });
      }

      return next.handle(request);
   }
}


export let fakeBackendProvider = {
   // use fake backend in place of Http service for backend-less development
   provide: HTTP_INTERCEPTORS,
   useClass: FakeBackendInterceptor,
   multi: true
};


```

Then, we need to put the response content available somewhere in the app. In this example, I've put it in the assets direcory. The content of the json file could be something like that:

```
[
    {
        id: 1
        name: 'John',
        surname: 'Rambo',
        birthdate: '1946/06/06'
    },
    {
        id: 2
        name: 'Sam',
        surname: 'Trautman',
        birthdate: '1929/07/04'
    }
]
```

Then, add the provider to your app.module.ts and thats all.
