    if (page < 0 || size < 0) {
        throw new ResponseStatusException(
                HttpStatus.BAD_REQUEST,
                "page and size must be greater than or equal to 0"
        );
    }
