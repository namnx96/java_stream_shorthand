# Shorthand Java stream for common usecases 

1: Convert Collection T to list of prop

    public static <T, R> List<R> mapApply(Collection<T> collection, Function<T, R> function) {
        return collection.stream()
                .map(function)
                .collect(Collectors.toList());
    }

    // map single prop of object and return List with expected size (lib: google collection)
    public static <T, R> List<R> mapApplyWithExpectedSize(Collection<T> collection, Function<T, R> function) {
        return collection.stream()
                .map(function)
                .collect(Collectors.toCollection(() -> Lists.newArrayListWithExpectedSize(collection.size())));
    }

    //example
    List<String> listStudentCode = StreamUtil.mapApply(listStudent, Student::getCode);


2: Filter Collection

    // filter list object by predicate
    public static <T> List<T> filterApply(Collection<T> collection, Predicate<T> predicate) {
        return collection
                .stream()
                .filter(predicate)
                .collect(Collectors.toList());
    }

    //example
    int boundary = 5;
    List<Student> listStudentAgeGreaterThan5 = StreamUtil.filterApply(listStudent, s -> s.getAge() > boundary);


3: Convert collection to stream of prop then filter

    public static <T, R> List<R> mapThenFilterApply(Collection<T> collection,
                                                    Function<T, R> function,
                                                    Predicate<R> predicate) {
        return collection.stream()
                .map(function)
                .filter(predicate)
                .collect(Collectors.toList());
    }

    public static <T, R> Set<R> mapThenFilterApplyToSet(Collection<T> collection,
                                                        Function<T, R> function,
                                                        Predicate<R> predicate) {
        return collection.stream()
                .map(function)
                .filter(predicate)
                .collect(Collectors.toSet());
    }
    
    //example
    List<String> listStudentCodeDifferent_NamNX = StreamUtil.mapThenFilterApply(
            listStudent,
            Student::getCode,
            s -> !"NamNX".equals(s)
    );


4: Convert Collection T to Map

    public static <V, K> Map<K, V> toMap(Collection<V> collection,
                                         Function<V, K> function) {
        return collection.stream()
                .collect(Collectors.toMap(function, Function.identity()));
    }
    
    //example: 
    Map<String, Student> mapStudentByCode = StreamUtil.toMap(listStudent, Student::getCode);

5: Convert Collection T to Map of List

    public static <T, K> Map<K, List<T>> groupingApply(Collection<T> collection, Function<T, K> function) {
        return collection
                .stream()
                .collect(Collectors.groupingBy(function));
    }

    //example:
    Map<Integer, List<Student>> mapListStudentByAge = StreamUtil.groupingApply(listStudent, Student::getAge);
    
6: Convert Collect T to Map of List of prop

    public static <T, K, V> Map<K, List<V>> groupingApply(Collection<T> collection,
                                                          Function<T, K> functionKey,
                                                          Function<T, V> functionValue) {
        return collection
                .stream()
                .collect(Collectors.groupingBy(functionKey,
                        Collectors.mapping(functionValue, Collectors.toList())));
    }
    
    //example:
    Map<Integer, List<String>> mapListStudentCodeByAge = StreamUtil.groupingApply(listStudent, Student::getAge, Student::getCode);

6: Shorthand to ignore using peek warning

    public static <T, R> Function<T, R> of(Function<T, R> function) {
        return function;
    }

    public static <T> Function<T, T> peek(Consumer<? super T> consumer) {
        return t -> {
            consumer.accept(t);
            return t;
        };
    }
    
    //example: increate age of student and map by code
    listStudent.stream()
               .map(StreamUtil.peek(s -> s.setAge(s.getAge() + 1)))
               .collect(Collectors.toMap(Student::getCode, Function.identity()));
            
            
