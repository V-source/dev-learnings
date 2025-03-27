```tsx

import React, { useRef, useState } from 'react';
import { StyleSheet, View, Text, Button } from 'react-native';
import PagerView from 'react-native-pager-view';

interface MyPagerWithButtonsProps {}

const MyPagerWithButtons: React.FC<MyPagerWithButtonsProps> = () => {
  const pagerRef = useRef<PagerView>(null);
  const [currentPage, setCurrentPage] = useState(0);
  const numberOfPages = 3; // Define el número total de páginas

  const goToPreviousPage = () => {
    if (currentPage > 0) {
      pagerRef.current?.setPage(currentPage - 1);
    }
  };

  const goToNextPage = () => {
    if (currentPage < numberOfPages - 1) {
      pagerRef.current?.setPage(currentPage + 1);
    }
  };

  const handlePageSelected = (event: any) => {
    setCurrentPage(event.nativeEvent.position);
  };

  return (
    <View style={styles.container}>
      <PagerView
        style={styles.pager}
        initialPage={0}
        ref={pagerRef}
        onPageSelected={handlePageSelected}
      >
        <View style={styles.page} key="1">
          <Text>First page</Text>
        </View>
        <View style={styles.page} key="2">
          <Text>Second page</Text>
        </View>
        <View style={styles.page} key="3">
          <Text>Third page</Text>
        </View>
      </PagerView>

      <View style={styles.buttonContainer}>
        <Button title="Anterior" onPress={goToPreviousPage} disabled={currentPage === 0} />
        <Button title="Siguiente" onPress={goToNextPage} disabled={currentPage === numberOfPages - 1} />
      </View>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
  },
  pager: {
    flex: 1,
  },
  page: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
  buttonContainer: {
    flexDirection: 'row',
    justifyContent: 'space-around',
    padding: 16,
  },
});

export default MyPagerWithButtons;
```
