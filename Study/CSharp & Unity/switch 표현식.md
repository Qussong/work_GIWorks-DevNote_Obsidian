기존의 switch 문보다 간결한 문법으로, **조건에 따라 값을 반환할 때 사용**한다.
switch 표현식은 기본적으로 값을 반환해야 하지만, 반환값이 필요 없는 경우에는 기존의 switch 문을 사용하는 것이 더 적합하다.

### 특징
1. 결과 반환 : 조건에 맞는 값을 바로 반환한다.
2. 간결함 : 중괄호 `{}` 와 `case` 키워드가 필요 없으며, `=>` 기호를 사용한다.
3. 기본값 제공 : \_(unrderscore)을 상요해 디폴트 케이스를 처리할 수 있다.

```csharp

```

### 동일한 값을 반환하는 경우
switch 표현식에서 여러 case가 동일한 값을 반환하는 경우, 공통된 조건을 그룹화하여 작성할 수 있다.
`or`키워드를 사용하여 여러 케이스를 하나의 그룹으로 묶어 같은 값을 반환하도록 작성할 수 있다.
```csharp
return page switch
{
    NJ_EUIPage.Home or NJ_EUIPage.Content => EAdminState.Start.ToString(),
    NJ_EUIPage.End => EAdminState.Quit.ToString(),
    _ => EAdminState.None.ToString(),
};
```
