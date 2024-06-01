1. Example of builder service
```java
ConvertedDocument convertedPdf = DocumentConverter.from(DocumentType.PNG)
                                                  .to(DocumentType.Pdf)
                                                  .convert();
```

2. Example of Rest Controller
```java

// inject bean
private final DomainHandler domainHanlder;

@PostMapping("/api/books")
public ResponseEntity<GenericResponse> createBooks(@Valid @RequestBody BookCreated bookCreated) {
    DomainPrcoessResponse response = domainHandler.handle(bookCreated);
    return EntityResponseCreator.from(response).create();
} 

```

3. Example of Service in layered-architecture
```java

// inject bean
private final BookRepository bookRepository;

public BookDto createBook(BookDto book) {
  Book bookEntity = book.toEntity();
  Book createdBookEntity = bookResponsity.save(bookEntity);
  return createdBookEntity.toDto();
} 

```
