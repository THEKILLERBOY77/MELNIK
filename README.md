# MELNIK
Реализация приближенного алгоритма для задачи коммивояжера (TSP)
Алгоритм Christofides' Algorithm (2-аппроксимация):
Алгоритм состоит из следующих шагов:

Найти минимальное остовное дерево (Minimum Spanning Tree).
Удвоить каждое ребро минимального остова.
Найти Эйлерово покрытие графа (Эйлеров цикл).
Преобразовать этот путь в гамильтонов цикл путём исключения повторяющихся вершин.
Вычислить длину полученного маршрута и сравнить её с нижними оценками.
Для реализации воспользуемся библиотеками Python networkx и scipy.

Шаг 1: Определение минимального остовного дерева
Минимальное остовное дерево строится методом Прима или Крускала. Используем метод Prim'a (networkx.minimum_spanning_tree()).

<img width="418" height="318" alt="image" src="https://github.com/user-attachments/assets/102cab30-741d-472d-b822-2c9fdb6d9366" />


# Создаем полный взвешенный граф
graph = nx.from_numpy_array(distance_matrix)


mst = nx.minimum_spanning_tree(graph)
print("Минимальное остовное дерево:", mst.edges(data=True))
Шаг 3: Дублирование рёбер
Создаем новый мультиграф, удваивая каждое ребро остова:


multi_graph = nx.MultiGraph()
for u, v, data in mst.edges(data=True):
    multi_graph.add_edge(u, v, weight=data['weight'])
    multi_graph.add_edge(v, u, weight=data['weight']) # дублируем обратное ребро
Шаг 4: Нахождение Эйлерова пути
Используем networkx.eulerian_circuit(), чтобы найти Эйлеров обход мультиграфа:


euler_path = list(nx.eulerian_circuit(multi_graph))
Шаг 5: Удаление повторяющихся узлов
Преобразуем полученный Эйлеров обход в Гамильтонов цикл, исключив повторы:


tour = []
visited = set()
for edge in euler_path:
    if edge[0] not in visited:
        tour.append(edge[0])
        visited.add(edge[0])
if len(tour) > 1 and tour[-1] != tour[0]:
    tour.append(tour[0]) # замыкаем цикл

print("Маршрут тура:", tour)
Шаг 6: Подсчет стоимости маршрута
Рассчитаем общую длину цикла:


total_cost = sum([distance_matrix[tour[i], tour[(i+1)%len(tour)]] for i in range(len(tour))])
print(f"Суммарная длина маршрута: {total_cost}")
Нижняя оценка оптимального решения
Нижней границей длины оптимального маршрута является сумма длин минимальных ребер, исходящих из каждой вершины. Мы можем оценить нижнюю границу следующим образом:


lower_bound = sum(sorted(distances)[1] for distances in distance_matrix)
print(f"Оценочная нижняя граница: {lower_bound}")
Соотношение найденного решения к нижней границе
R
a
t
i
o
=
Общая длина маршрута
Нижняя граница
Ratio= 
Нижняя граница
Общая длина маршрута
​
 
Подставляем значения и вычисляем отношение:


ratio = total_cost / lower_bound
print(f"Соотношение к нижней границе: {ratio:.2f}")
