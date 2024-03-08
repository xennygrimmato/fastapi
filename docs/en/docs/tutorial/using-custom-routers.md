from fastapi import APIRouter

class CustomRouter(APIRouter):
    def add_api_route(self, path: str, endpoint: Callable, **kwargs):
        # Custom logic before adding a route
        print(f"Adding route: {path}")
        super().add_api_route(path, endpoint, **kwargs)
```

## Initializing FastAPI with a Custom Router

When initializing your FastAPI application, you can specify your custom router class using the `router_class` parameter in the `FastAPI` constructor.

```python
from fastapi import FastAPI
from my_routers import CustomRouter

app = FastAPI(router_class=CustomRouter)

@app.get("/")
async def main():
    return {"message": "Hello World"}
```

## Using Custom Parameters in Route Decorators

To use custom parameters in route decorators, you can extend the `APIRoute` class. This allows you to capture additional parameters in your route decorators and use them within your route handlers.

```python
from fastapi.routing import APIRoute

class CustomAPIRoute(APIRoute):
    def __init__(self, path: str, endpoint: Callable, custom_param: str = "", **kwargs):
        self.custom_param = custom_param
        super().__init__(path, endpoint, **kwargs)

    def get_route_handler(self) -> Callable:
        original_route_handler = super().get_route_handler()
        async def custom_route_handler(*args, **kwargs):
            print(f"Custom Param: {self.custom_param}")
            return await original_route_handler(*args, **kwargs)
        return custom_route_handler
```

To use this custom route in your application, you need to specify it when adding routes:

```python
app.router.route_class = CustomAPIRoute

@app.get("/custom", custom_param="example")
async def custom_route():
    return {"message": "This is a custom route"}
```

## Accessing Custom Router Attributes in Route Handlers

You can access custom attributes or methods defined in your custom router class from within your route handlers. This is useful for sharing common functionalities or data across routes.

```python
class CustomRouter(APIRouter):
    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)
        self.custom_attribute = "example"

@app.get("/use_custom_attribute")
async def use_custom_attribute():
    router = request.app.router
    return {"custom_attribute": router.custom_attribute}
